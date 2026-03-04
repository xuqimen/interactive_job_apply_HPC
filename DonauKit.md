# 功能架构
DonauKit和Hyper IO在整个HPC中的功能架构如图1所示。当前版本继续实现DonauKit和Hyper IO的功能交付，提升其竞争力。Donau Portal主要实现应用中心与报表，支持作业提交、作业管理、应用集成、远程可视化、报表展示、计费等核心功能，对外提供简单易用的界面。Donau Scheduler是华为全自研的调度器，提供大规模集群下的高资源利用率、高吞吐量的作业调度能力。Hyper IO实现IO转发以及AdhocFS等能力，使能HPC等场景下的应用IO加速。


DonauKit套件不对第三方开源软件、OS和周边配套产品（存储、网络等）的安全风险与漏洞负责。

图1 HPC功能框架
点击放大

上图中的“多瑙管理平台”对应“Donau Portal”，“多瑙调度器”对应“Donau Scheduler”。


提交作业
dsub命令行选项用于描述作业的定制需求和行为方式。不同的选项控制作业不同管理阶段的属性。

作业提交
作业提交时，可以使用不同的交互方式。多瑙支持：
批处理提交：dsub默认提交方式。提交作业成功，dsub命令返回。
阻塞式提交（-w | -wo）：dsub提交作业后显示作业的调度执行过程，直到作业完成。
交互式提交（-I）：dsub提交作业后等待作业执行，作业执行后显示字符终端交互界面。
作业调度
作业调度时，调度器根据作业需求分配计算资源。计算资源包括CPU，内存，GPU等。根据作业及其处理数据的不同，提交命令可设置不同的计算资源需求。最简单的使用方式是描述计算资源的数量（-R | --resource）。对于复杂的作业资源需求，可描述资源副本数量（-N | --replica），计算资源在单节点上的拓扑（-a | --affinity），计算资源在多个节点上的分布（-nn | --nnodes和-rpn | --replica-per-node），以及计算资源的候选节点选择（-nl | --node-list）和排序（-O | --order）等。

作业运行
作业运行时，提交命令行选项设定了运行时环境。作业运行前后，提供执行前后处理（--prehook和--posthook）脚本的能力，帮助用户执行作业环境的准备和清理工作。提供重定向标准输出（-o | --output）和标准出错（-e | --error）信息导出到文件中，帮助用户更好管理作业输出信息。

dsub命令还有更多选项帮助用户更好设置作业的基本属性，调度属性，资源属性和运行时属性，详细请参见《Kunpeng DonauKit 24.0.0 命令参考》中“Donau Scheduler命令参考 > 版本命令 > 作业提交dsub”。

# dsub
用户通过dsub提交作业时可指定参数，常用参数说明如表1所示。

表1 常用参数说明
参数

说明

--prehook

--prehook用于提交作业时指定preHook的执行命令，preHook是为作业执行前进行准备工作。
--posthook用于提交作业时，指定postHook的执行命令，postHook是为作业执行后进行收尾工作。
preHook&postHook有超时时间、重试次数等限制，具体请参见preHook&postHook使用说明。
postHook运行期间，不允许重启作业。
支持将postHook执行过程纳入作业生命周期。
postHook运行期间，作业状态为RUNNING，资源不释放。
postHook运行结束，作业进入完成态。作业执行成功/失败由任务执行结果决定；postHook执行结果不影响作业的状态。
postHook运行期间，不允许终止、挂起、恢复、创建检查点、重启、重新提交、修改、抢占和回收作业，允许作业被强制终止。
须知：
preHook命令执行结束后作业开始执行。如果preHook在执行过程中启动了进程，该进程可以一直在后台存在，直到进程结束或作业运行结束。
如果postHook在执行过程中启动了后台进程，postHook需要等待所有的后台进程结束后再结束。
--posthook

-n | --name

提交作业时，指定作业的名称。

-A | --account

提交作业时，提交作业到指定Account下。

-q | --queue

提交作业时，提交作业到指定队列下。

-N | --replica

提交作业时，指定作业的资源副本数量。

-R | --resource

提交作业时，指定作业的资源需求。

-mR | --min-resource

提交作业时，指定作业的最小资源需求。

--mpi

提交MPI作业时，指定作业的类型，Donau Scheduler会根据指定的作业类型来调度执行MPI作业。

-rpn | --replica-per-node

提交作业时，指定作业在每个节点运行的最大任务数。

-nn | --nnodes

提交作业时，指定作业运行的节点数。

-nl | --node-list

提交作业时，指定节点范围或偏好。

支持表达式[]和!，[]表示范围，!表示排除。若包含!，则整体必须使用单引号引起来。
支持关键字“others”：作业在指定的节点或资源池上不满足运行条件时，可以运行在其他节点。
--label

提交作业时，指定标签，Donau Scheduler会根据指定标签来分配任务在指定主机节点上运行。

-o | --output

提交作业时，重定向该作业的标准输出日志路径。-o以追加写入的方式，-oo以覆盖写入的方式。

绝对路径支持使用%U表示userName，%J表示JobID，%G表示ResourceGroupName，%I表示Index，%A表示UserName_JobID_ResourceGroupName_Index。

-oo | --output-override

-e | --error

提交作业时，重定向该作业的错误输出日志路径。-e以追加写入的方式，-eo以覆盖写入的方式。

绝对路径支持使用%U表示userName，%J表示JobID，%G表示ResourceGroupName，%I表示Index，%A表示UserName_JobID_ResourceGroupName_Index。

-eo | --errlog-overide

-s | --script

提交作业时，以脚本方式提交作业。

--topology

提交作业时，指定作业调度的网络拓扑信息。

-jr | --job-requeue

提交作业时，指定作业重排队次数和退出码范围。

--container

提交作业时，指定作业运行的容器。

--array

提交一个数组作业，并指定数组的大小。

-ar | --adv-reservation

提交作业时，指定需要使用的预约。

-h | --help

用于指导dsub各个命令的使用规则以及具体功能，返回支持的参数信息。