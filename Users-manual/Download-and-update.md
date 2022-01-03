# 下载与更新

> *译自：[Download and update](https://github.com/sqlmapproject/sqlmap/wiki/Download-and-update)*

点击[这里](https://github.com/sqlmapproject/sqlmap/tarball/master)下载最新版本的 tar 源码包，或者点击[这里](https://github.com/sqlmapproject/sqlmap/zipball/master)下载最新版本的 zip 源码包。

更好的方式是，直接使用 Git 克隆 sqlmap 仓库。[Git](https://github.com/sqlmapproject/sqlmap)：

    git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev

或者，你可以从 [PyPI](https://pypi.org/project/sqlmap/) 仓库获取最新的（按月更新）软件包：

    pip install --upgrade sqlmap

在任意时刻，你可以通过下面的方式获取最新版本：

    python sqlmap.py --update

或者：

    git pull
