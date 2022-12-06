# PlantIT Example: Bind Mounts

A basic PlantIT workflow demonstrating the use of [bind mounts](https://apptainer.org/docs/user/main/bind_paths_and_mounts.html#bind-paths-and-mounts).

## What's a bind mount?

By default Singularity (now [Apptainer](http://apptainer.org/)) only mounts [the current working directory and a few other paths](https://apptainer.org/docs/user/main/bind_paths_and_mounts.html#system-defined-bind-paths) selected by system administrators. The rest of the container filesystem is readonly. Some workflows may need to write to different locations within the container, however. The `--bind` option allows mapping additional paths in the container to writable locations on the host, e.g.

```
singularity shell --bind /data:/mnt docker://alpine
```

The above maps `/data` on the host to `/mnt` within the container, and files created in `/mnt` will exist on the host after the container exits.

## Usage

PlantIT supports bind mounts. To mount additional locations inside your container, provide one or more paths to the `mount` attribute in your `plantit.yaml` file. For instance:

```
...
mount:
  - /data
  - /logs
...
```

This will mount `/data` and `/logs` within the container to eponymously named subdirectories of the host working directory, in this case `<task workdir>/data` and `<task workdir>/logs`.

## Outputs

If you would like files written to mounted locations to be transferred back to CyVerse as workflow outputs, you have two options:

1. Set your workflow's output path to a bind mount path relative to the task working directory

For instance:

```
...
mount:
  - /data
output:
  path: data
...
```

2. Copy output files from mount points back to the task working directory from your workflow

The `$WORKDIR` environment variable is available to your workflow's container runtime. Simply copy any nested output files back to the working directory and configure the [`output` section](https://plantit.readthedocs.io/en/latest/md/concepts/workflows.html#outputs) in `plantit.yaml` appropriately to include desired names/patterns.
