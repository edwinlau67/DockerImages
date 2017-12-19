# DockerImages
Collection of Dockerfile for build Docker images

## List of Docker Images
- [msbuild2017/v15.4](msbuild2017/v15.4)
  - VS2017 Build Tools Version 15.4
  - Build image, eg. `docker build -t <docker_hub_account>/msbuild2017:v15.4 .`
  - Create and Run a container, eg. `docker run -it -m 2GB --name buildtools <docker_hub_account>/msbuild2017:v15.4 cmd`

## References
- [Install Build Tools into a Container](https://docs.microsoft.com/en-us/visualstudio/install/build-tools-container)
- [Advanced Example for Containers](https://docs.microsoft.com/en-us/visualstudio/install/advanced-build-tools-container)
- [Visual Studio Build Tools 2017 component directory](https://docs.microsoft.com/en-us/visualstudio/install/workload-component-id-vs-build-tools)

