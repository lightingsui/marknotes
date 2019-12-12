> 转载： [逸宸a](https://www.jianshu.com/u/9d6920d486e7)关注   原文地址：<docker容器镜像的备份恢复https://www.jianshu.com/p/8533b6838215> 

## docker容器镜像的备份恢复

#### 1. 备份容器

首先，为了备份Docker中的容器，我们会想看看我们想要备份的容器列表。要达成该目的，我们需要在我们运行着Docker引擎，并已创建了容器的Linux机器中运行 docker ps 命令。

```bash
docker ps
```

在此之后，我们要选择我们想要备份的容器，然后去创建该容器的快照。我们可以使用 docker commit 命令来创建快照。 

```bash
docker commit -p 30b8f18f20b4 container-backup
```

该命令会生成一个作为Docker镜像的容器快照，我们可以通过运行 `docker images` 命令来查看Docker镜像，如下。 

```bash
docker images
```

正如我们所看见的，上面做的快照已经作为Docker镜像保存了。现在，为了备份该快照，我们有两个选择，一个是我们可以登录进Docker注册中心，并推送该镜像；另一个是我们可以将Docker镜像打包成tar包备份，以供今后使用。

如果我们想要在[Docker注册中心](https://links.jianshu.com/go?to=https%3A%2F%2Fregistry.hub.docker.com%2F)上传或备份镜像，我们只需要运行 docker login 命令来登录进Docker注册中心，然后推送所需的镜像即可。

```bash
docker login -u 用户名
```

```bash
docker tag a25ddfec4d2a arunpyasi/container-backup:test #给imageID=a25ddfec4d2a打个标签，命名要和服务器一致，tag标签版本号可以不一致
docker push arunpyasi/container-backup #推送到服务器 ，名称要和服务器一致
```

如果我们不想备份到docker注册中心，而是想要将此镜像保存在本地机器中，以供日后使用，那么我们可以将其作为tar包备份。要完成该操作，我们需要运行以下 `docker save` 命令。 

```bash
docker save -o ~/container-backup.tar container-backup
```

要验证tar包是否已经生成，我们只需要在保存tar包的目录中运行 ls 命令即可。 

#### 2. 恢复容器

接下来，在我们成功备份了我们的Docker容器后，我们现在来恢复这些制作了Docker镜像快照的容器。如果我们已经在注册中心推送了这些Docker镜像，那么我们仅仅需要把那个Docker镜像拖回并直接运行即可。

```bash
docker pull arunpyasi/container-backup:test
```

但是，如果我们将这些Docker镜像作为tar包文件备份到了本地，那么我们只要使用 docker load 命令，后面加上tar包的备份路径，就可以加载该Docker镜像了。 

```bash
docker load -i ~/container-backup.tar
```

现在，为了确保这些Docker镜像已经加载成功，我们来运行 docker images 命令。

```bash
docker images
```

在镜像被加载后，我们将用加载的镜像去运行Docker容器。

```bash
docker run -d -p 80:80 container-backup
```

#### 3. 迁移Docker容器

迁移容器同时涉及到了上面两个操作，备份和恢复。我们可以将任何一个Docker容器从一台机器迁移到另一台机器。在迁移过程中，首先我们将把容器备份为Docker镜像快照。然后，该Docker镜像或者是被推送到了Docker注册中心，或者被作为tar包文件保存到了本地。如果我们将镜像推送到了Docker注册中心，我们简单地从任何我们想要的机器上使用 docker run 命令来恢复并运行该容器。但是，如果我们将镜像打包成tar包备份到了本地，我们只需要拷贝或移动该镜像到我们想要的机器上，加载该镜像并运行需要的容器即可。