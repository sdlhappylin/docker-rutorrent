[linuxserverurl]: https://linuxserver.io
[forumurl]: https://forum.linuxserver.io
[ircurl]: https://www.linuxserver.io/irc/
[podcasturl]: https://www.linuxserver.io/podcast/
[appurl]: https://github.com/Novik/ruTorrent
[hub]: https://hub.docker.com/r/linuxserver/rutorrent/

[![linuxserver.io](https://raw.githubusercontent.com/linuxserver/docker-templates/master/linuxserver.io/img/linuxserver_medium.png)][linuxserverurl]

[LinuxServer.io][linuxserverurl] 团队为您带来的另一个容器版本，其中包含简单的用户映射和社区支持。 向我们寻求支持:
* [forum.linuxserver.io][forumurl]
* [IRC][ircurl] on freenode at `#linuxserver.io`
* [Podcast][podcasturl] 涵盖Linux服务器大多数做到的事情，专注于Docker和容器化。

# linuxserver/rutorrent
[![](https://images.microbadger.com/badges/version/linuxserver/rutorrent.svg)](https://microbadger.com/images/linuxserver/rutorrent "在 microbadger.com获取版本标识")[![](https://images.microbadger.com/badges/image/linuxserver/rutorrent.svg)](https://microbadger.com/images/linuxserver/rutorrent "在 microbadger.com 上获取图标文件")[![Docker Pulls](https://img.shields.io/docker/pulls/linuxserver/rutorrent.svg)][hub][![Docker Stars](https://img.shields.io/docker/stars/linuxserver/rutorrent.svg)][hub][![Build Status](https://ci.linuxserver.io/buildStatus/icon?job=Docker-Builders/x86-64/x86-64-rutorrent)](https://ci.linuxserver.io/job/Docker-Builders/job/x86-64/job/x86-64-rutorrent/)

rtorren的WebUI通用版本。 [Rutorrent](https://github.com/Novik/ruTorrent)

[![rutorrent](https://raw.githubusercontent.com/linuxserver/docker-templates/master/linuxserver.io/img/rutorrent.jpg)][appurl]
已将nginx.conf文件中的端口修改为8010，并优化utorrent.rc文件。

## 用法

```
docker create --name=rutorrent \
-v <path to data>:/config \
-v <path to downloads>:/downloads \
-e PGID=<gid> -e PUID=<uid> \
-e TZ=<timezone> \
-p 8010:8010 -p 5000:5000 \
-p 51413:51413 -p 6881:6881/udp \
linuxserver/rutorrent
```

## 参数

`
参数两部分，用冒号分隔，左侧为宿主机，右侧为容器。
例如，使用端口-p 外部：内部 - 表示的是从容器的内部到外部的端口映射。
这样 -p 8080：80参数会把容器内的80端口暴露在宿主机的8080端口上，容器内运行在80端口的应用可以通过http：//192.168.x.x：8080访问。
`


* `-p 8010` - web端口
* `-p 5000` - tcp 端口
* `-p 51413` - tcp端口
* `-p 6881/udp` - udp端口
* `-v /config` - rutorrent配置文件目录
* `-v /downloads` - 下载目录
* `-e PGID` for GroupID - 参照下面的说明
* `-e PUID` for UserID - 参照下面的说明
* `-e TZ` 设置时区, 例如 Asia/Shanghai

基于alpine linux 的s6 overlay，在shell执行`docker exec -it rutorrent / bin / bash`来访问容器。

### User / Group 标识符

使用数据卷 (`-v` 参数) 时，宿主机和容器间偶尔会出现权限问题，这个问题可以通过指定用户标识`PUID` 以及 组标识`PGID`来避免。确保使用指定的用户拥有宿主机数据卷目录权限，并保证“正常工作”™。 
在这个例子中 `PUID=1001` 和 `PGID=1001`. 找到的 `id user` 如下：

```
  $ id <dockeruser>
    uid=1001(dockeruser) gid=1001(dockergroup) groups=1001(dockergroup)
```

## 设置应用

Webui 位于 `<your-ip>:8010` , rtorrent 位于 /config/rtorrent, php配置文件位于 config/php ， webui 配置文件位于 /config/rutorrent/settings.

`用户在ruTorrent设置面板中修改的设置直到重启时都是有效的。之后所有设置将根据rtorrent配置文件（/config/rtorrent/rtorrent.rc）设置，这是应用程序本身的限制。`

**为方便unraid用户使用，将web端口由80改为8010 **

`** 还应该注意的是，该容器在运行时在/downloads目录下创建incoming、completed、watched三个子目录。**`

** 端口分配和配置文件夹结构已经从此容器的以前基于ubuntu的版本中更改，我们建议进行全新安装 **

可以通过在/config/rtorrent/rtorrent.rc文件中修改`system.umask.set`的值来设置Umask

如果你看到这个错误`Caught internal_error: 'DhtRouter::get_tracker did not actually insert tracker.'`'，可以通过在`/ config / rtorrent / rtorrent.rc`文件中禁用dht来解决，具体如下所示：

```shell
dht = disable
peer_exchange = no
```

## 信息

* 容器运行时访问命令: `docker exec -it rutorrent /bin/bash`
* 实时监控容器日志: `docker logs -f rutorrent`

* 容器版本号

`docker inspect -f '{{ index .Config.Labels "build_version" }}' rutorrent`

* 镜像版本号

`docker inspect -f '{{ index .Config.Labels "build_version" }}' linuxserver/rutorrent`


## 版本号

+ **08.12.17:** Rebase to alpine 3.7, add sox package.
+ **28.10.17:** Mediainfo moved from testing to community repo.
+ **09.10.17:** Use repo version of mediainfo to shorten build time.
+ **28.05.17:** Fix permissions on secondary temp folder of nginx.
+ **26.05.17:** Rebase to alpine 3.6.
+ **03.05.17:** Fix log permissions.
+ **18.03.17:** Note in readme about disabling dht in some circumstances.
+ **24.02.17:** Patch a source file to quash rss https bug.
+ **29.01.17:** Rebase to alpine 3.5.
+ **20.11.16:** Add php7-mbstring package, bump mediainfo to 0.7.90.
+ **14.10.16:** Add version layer information.
+ **04.10.16:** Remove redundant sessions folder.
+ **30.09.16:** Fix umask.
+ **21.09.16:** Bump mediainfo, reorg dockerfile, add full wget package.
+ **09.09.16:** Add layer badges to README.
+ **28.08.16:** Add badges to README, bump mediainfo version to 0.7.87
+ **07.08.16:** Perms fix on nginx tmp folder, also exposed php.ini for editing by user
in /config/php.
+ **26.07.16:** Rebase to alpine.
+ **08.03.16:** Intial Release.
