# Container Customization Directory

This directory is for your own personal customizations to the `Dockerfile`,
and the files it will add to the container.

The structure is like:

```
customizations
├── pre-base.dockerfile
├── post-base.dockerfile
├── pre-build.dockerfile
├── post-build.dockerfile
├── pre-install.dockerfile
├── post-install.dockerfile
└── <any other files you need>
```

Each `dockerfile` is inserted into the main `Dockerfile` at the stages
indicated by the names, which are executed in the order listed above.

The stages are:

- `pre-base.dockerfile` happens before anything else
  - Note: Anything you need to do to enable `apt-get` should go here
- `base` installs core and runtime dependencies
- `post-base` happens just after the `base` image is finalized
  - Additions here apply to both `build` and `install`
- `pre-build` happens just before `build`, and only applies to `build`
- `build` compiles and builds the software
- `post-build` happens just after the software builds
  - Only patches in `/opt` survive this phase
  - Use this phase for post-build patches to the software
- `pre-install` happens just before installing into the main image
- `install` installs the software into the base image and links it
- `post-install` happens at the very end, when the container is ready to go
  - This is usually the best place to run a post-installation script
