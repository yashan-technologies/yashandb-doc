## Python环境准备

通用Python应用程序连接YashanDB需要先安装并配置Python开发环境，YashanDB Python驱动支持3.6.0及以上版本的Python。

YashanDB Python驱动支持Windows和Linux平台，本手册将分别介绍YashanDB Python驱动在这两个平台环境下的安装配置过程。

**下载并安装Python开发环境**

在官方路径中下载并安装符合上述要求版本的Python包，自行配置Python环境。

**Linux平台验证Python环境**

配置完成后，通过运行python3 --version验证Python环境是否正常：

```shell
# python3 --version
Python 3.6.15
```

**Windows平台验证Python环境**

配置完成后，通过运行python --version验证Python环境是否正常：

```bash
C:\>python --version
3.6.15
```

YashanDB Python驱动包的安装需要用到pip。正常情况下pip已经内置在Python中，所以不需要再次安装。两个平台均可通过运行pip3 --version验证是否安装pip：

```shell
pip3 --version
```

## 安装依赖项

使用YashanDB Python驱动需要先安装和配置YashanDB客户端获取YashanDB C驱动库，操作过程请参考安装手册[YashanDB客户端安装](../../安装和升级/安装部署/YashanDB客户端安装/00YashanDB客户端安装)章节。

## YashanDB Python驱动包安装

1.依据安装手册[YashanDB软件包清单](../../安装和升级/安装部署/安装前准备/下载软件包)获取平台对应的软件包，建议选择yasdb-*版本号*-py3-none-any.whl。本手册以1.0.2版本为例进行介绍。

> **Note**:
>
> 如需选用yaspy驱动包，Windows平台应选择yaspy-xx.xx-cp36-cp36m-win_amd64.whl，Linux平台应选择yaspy-xx.xx-cp36-cp36m-linux-x86_64.whl或yaspy-xx.xx-cp36-cp36m-linux-aarch_64.whl。

2.将压缩包下载到本地路径，如/path/YASDB Python。

3.两个平台均可使用运行pip3 install *软件包名称* 指令对驱动包进行安装。

**Linux平台安装驱动**

```bash
pip3 install yasdb-1.0.2-py3-none-any.whl
```

> **Note**：
>
> Linux平台安装驱动时需先切换至root用户。

**Windows平台安装驱动**

```shell
pip3 install yasdb-1.0.2-py3-none-any.whl
```

显示安装成功后，Python应用程序可通过引用yasdb模块的方式对YashanDB数据库的进行访问操作。
