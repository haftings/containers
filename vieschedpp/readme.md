# Containerization for VieSched++

This `Dockerfile` will build a container (default name `vieschedpp`) to hold VieSched++, the VieSched++ GUI, and the VieSched++ AUTO tool.

The container is based on Ubuntu 20.04 and currently weighs in at just under 1 GiB once compiled.

It should work on any Linux-like system with either `podman` or `docker` installed.

## Instructions

1. Build the container

   ```bash
   git clone https://github.com/haftings/containers.git
   cd containers/vieschedpp
   podman build src -t vieschedpp
   ```

2. *(optional)* Clean up left-over build layers

   ```bash
   podman image prune
   ```

   *(Answer `y` when asked `WARNING! This command removes all dangling images. Are you sure you want to continue? [y/N]`.)*

3. Use the included `run-vieschedpp-container` script to enter a fresh container

   ```bash
   ./run-vieschedpp-container
   ```

4. Inside the container, start VieSched++, the GUI wrapper, or the AUTO wrapper

   ```bash
   vieschedppgui
   ```
## Advanced Instructions

It's very common to run containers on a remote system, just throw a `--host` argument onto `run-vieschedpp-container`:

```bash
./run-vieschedpp-container --host somehost
```

The default is to mount `master` and `catalogs` directories read-only, but if you want to have the program update them instead of giving an error, you'll want to make them writeable with `-p rw,Z`.

```bash
./run-vieschedpp-container --host somehost -p rw,Z
```

If you're compiling in an environment that needs custom proxy, DNS, NTP, repo, certificate, or other settings, then use the [`customizations` directory](customizations/readme.md) and the `customize-container` script:

```bash
customize-container
podman build src -t vieschedpp -f custom.dockerfile
```

## Compatibility Notes

- X11 is required on the client, but X11 is ***not*** required on the host running container
  - E.g. you can run the container headless and still use VieSched++ GUI connecting from an X11-enabled client
- In theory, it should work on any x86_64 Linux or Linux-like OS that supports containers
  - I've currently tested running the container with `podman` on a RHEL9 / Rocky9 VM on an Apple Silicon Mac with an M2 Pro
  - Planned testing includes RHEL9 / Rocky9 on x86_64, and Ubuntu 22.04 on x86_64 and Apple Silicon

## Testing Matrix

OS             | Architecture          | Disposition
---------------|-----------------------|--------------------------------------
RHEL9 / Rocky9 | arm64 (Apple Silicon) | <span style="color:green">Tested and working</span>
RHEL9 / Rocky9 | x86_64                | <span style="color:darkgreen">Plan to test, probably works</span>
Ubuntu 22.04   | arm64 (Apple Silicon) | <span style="color:darkgreen">Plan to test, probably works</span>
Ubuntu 22.04   | x86_64                | <span style="color:darkgreen">Plan to test, probably works</span>
Other          | x86_64 / arm64        | <span style="color:grey">No plans to test, probably works</span>
Various        | Other                 | <span style="color:darkred">No plans to test, probably won't work</span>
