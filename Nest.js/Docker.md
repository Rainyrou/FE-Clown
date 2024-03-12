在无 Docker 的情况下，部署后端服务（包括中间件如 MySQL、Redis 等）通常需要手动在每台机器上安装依赖、配置环境变量、启动服务等步骤，这不仅耗时还容易出错，特别是在需要部署多个实例时

Docker 通过以下方式解决这一问题：

- Docker 容器使应用的开发、测试和生产环境保持一致，减少了因环境差异而导致的问题
- Docker 容器可快速启动和停止，灵活部署和扩展服务。使用 Docker 镜像可确保每个实例都使用相同的配置部署，减少了人为操作失误
- 更新应用或其依赖时，只需更新 Docker 镜像并重新部署容器即可，这简化了更新过程，并允许快速回滚到旧版本
- Docker 容器共享宿主机的操作系统内核，而无需为每一应用实例运行一个完整的操作系统，这提高了资源利用率，降低开销

Docker 是一个开源的应用容器引擎，它允许开发者打包应用及应用的依赖包到一个可移植的容器中，然后发布到任何支持 Docker 的平台上，无论是物理机、虚拟机、云平台还是本地。Docker 利用 Linux 内核的多种功能，如 cgroups 和 namespace，来实现资源和工作环境的隔离

- Image：一个轻量、可执行的独立软件包，包含运行应用所需的所有内容，包括代码、运行时环境、库、环境变量和配置文件
- Container：镜像的运行实例，它在 Docker 引擎上运行，提供一个隔离的环境，但比传统的虚拟机更轻量，因为容器共享宿主机的内核而无需模拟整个操作系统
- Registry：存放 Docker 镜像的地方。Docker Hub 是 Docker 的公共仓库，用户可以从中下载镜像或上传自己的镜像