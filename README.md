# interact

A shell script for quickly requesting an interactive job on a DonauKit-managed HPC cluster.

## Requirements

- `bash`
- `dsub` (DonauKit scheduler CLI) available in `PATH`

## Installation

Place `interact` somewhere on your `PATH` and make it executable:

```bash
chmod +x interact
cp interact ~/.local/bin/
```

## Usage

```
interact [OPTION]...
```

| Option | Short | Default | Description |
|---|---|---|---|
| `--nodes NUM` | `-n` | `1` | Number of nodes to request |
| `--queue QUEUE` | `-q` | `q_genuser` | Queue to submit to |
| `--name NAME` | | `interQX` | Job name |
| `--rpn NUM` | | `608` | Max replicas per node |
| `--help` | `-h` | | Show help and exit |
| `--version` | | | Show version and exit |

## Examples

```bash
# 1 node on the default queue (q_genuser)
interact

# 2 nodes on the default queue
interact -n 2

# 1 node on a specific queue
interact -q q_gpu

# 4 nodes on a specific queue
interact -n 4 -q q_large
```

## Author

Qimen Xu <qimenxu@foxmail.com>