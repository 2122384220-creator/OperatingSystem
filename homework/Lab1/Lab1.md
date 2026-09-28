# Lab1：操作系统实验环境验收

> **作业目标**：用命令输出和截图证明 VMware、Ubuntu、网络、虚拟硬件以及课程必需的 C 开发工具链已正确安装，并成功编译运行第一个 C 程序。
>
> **前置条件**：已按 [`操作手册.md`](操作手册.md) 完成虚拟机安装、国内软件源配置和开发工具链配置。
>
> **命令执行方式**：下面所有命令请**一条一条执行**，每执行一条就先看清它的输出再执行下一条。不要把一节里的命令一次性全部粘贴进终端，否则输出会混在一起，看不出是哪条命令出了问题。

---

## 任务一：检查 VMware 版本

### 第一步：查看版本

在 Windows 中打开 VMware Workstation Pro，在菜单中选择 **Help → About VMware Workstation**，查看版本信息。

教师指定版本为：

```text
VMware Workstation Pro 26H1 for Windows
```

### 第二步：填写检查结果

| 项目 | 你的填写内容 |
| :--- | :--- |
| 已安装的 VMware 完整版本号 | |
| 是否为教师指定版本 | |

![VMware 版本](imgs/lab1-vmware-version.png)

---

## 任务二：检查 Ubuntu 版本

### 第一步：查看当前系统版本

```bash
cat /etc/os-release
```

验收标准：`PRETTY_NAME` 中同时包含 `Ubuntu 24.04` 和 `LTS`。安装后执行过系统更新时，小版本可能高于 `24.04.4`，这属于正常现象。

### 第二步：查看处理器架构

```bash
uname -m
```

验收标准：输出为 `x86_64`，对应 amd64 安装镜像。

### 第三步：查看安装介质版本

```bash
sudo cat /var/log/installer/media-info
```

验收标准：输出中包含 `Ubuntu 24.04.4 LTS`，说明安装时使用的是教师提供的 24.04.4 镜像。

### 第四步：填写检查结果

| 项目 | 你的填写内容 |
| :--- | :--- |
| Ubuntu 当前完整版本 | |
| 安装介质的版本 | |
| 处理器架构 | |
| 是否为教师提供的 Ubuntu 24.04.4 LTS Desktop amd64 | |

![Ubuntu 版本](imgs/lab1-ubuntu-version.png)

---

## 任务三：检查虚拟机联网

### 第一步：查看 IP 地址

```bash
hostname -I
```

期望输出一个私有 IP 地址，通常以 `192.168`、`172` 或 `10` 开头。

### 第二步：查看默认路由

```bash
ip route
```

期望能看到一行包含 `default via` 的默认路由，说明虚拟机知道该把外网流量发给哪个网关。

### 第三步：测试 IP 联通性

```bash
ping -c 4 223.5.5.5
```

成功说明虚拟机可以通过 NAT 访问外部网络。

### 第四步：测试 DNS 解析

```bash
ping -c 4 mirrors.tuna.tsinghua.edu.cn
```

成功说明 DNS 解析和域名网络访问正常。

### 第五步：确认软件源可用

```bash
sudo apt update
```

期望 `Get:` 行都来自你配置的国内镜像站（例如 `mirrors.tuna.tsinghua.edu.cn`），并且没有 `Err:` 或 `Failed` 提示。

> 如果所在网络禁止 ping，但 `sudo apt update` 能正常下载软件索引，可将 `sudo apt update` 的成功输出作为联网证据，并在表格中说明情况。

### 第六步：填写检查结果

| 项目 | 你的填写内容 |
| :--- | :--- |
| 虚拟机 IP 地址 | |
| 网络模式 | NAT / 其他： |
| ping `223.5.5.5` 是否成功 | |
| ping `mirrors.tuna.tsinghua.edu.cn` 是否成功 | |
| 使用的软件源镜像站 | |
| `sudo apt update` 是否成功 | |
| 联网是否合格 | |

![虚拟机联网](imgs/lab1-network.png)

---

## 任务四：检查 CPU、内存和存储分配

### 第一步：查看 CPU 核心数

```bash
nproc
```

输出应与安装手册第三节选定的配置档位一致，即 `2` 或 `4`。虚拟机至少应有 2 核。

### 第二步：查看内存

```bash
free -h
```

看 `Mem` 行的 `total` 列。显示的总内存通常会略小于 VMware 中设置的数值，因为一部分内存被固件和内核占用。虚拟机至少应有 4GB 内存。

### 第三步：查看磁盘设备

```bash
lsblk
```

看虚拟磁盘（通常是 `sda` 或 `nvme0n1`）的 `SIZE` 列，应与创建虚拟机时设置的虚磁盘上限一致，即 40GB、60GB 或 80GB。虚拟机至少应有 40GB 虚磁盘。

### 第四步：查看根分区容量

```bash
df -h /
```

看 `Size` 和 `Avail` 列。根文件系统容量可能略小于虚磁盘上限，属于正常现象。

### 第五步：填写检查结果

| 项目 | 你的填写内容 |
| :--- | :--- |
| 宿主机内存 / CPU 核心 / 存放盘剩余空间 | |
| 选择的配置档位 | 最低可用档 / 课程推荐档 / 宽裕档 |
| 虚拟 CPU 核心数 | |
| 虚拟内存 | |
| 虚磁盘容量 | |
| 根分区可用空间 | |
| 资源分配是否符合对应档位 | |

![虚机资源](imgs/lab1-resources.png)

---

## 任务五：检查开发工具链并运行第一个 C 程序

### 第一步：确认软件包已安装

```bash
dpkg-query -W -f='${Package}\t${Version}\n' open-vm-tools build-essential gdb git manpages-dev
```

期望每行都输出各自的已安装版本号。某一行没有版本号或提示未安装，说明该软件包缺失。

### 第二步：查看 VMware Tools 版本

```bash
vmware-toolbox-cmd -v
```

期望输出一个版本号，例如 `12.x.x.xxxxx (build-xxxxxxx)`。

### 第三步：确认 `open-vm-tools` 服务在运行

```bash
systemctl is-active open-vm-tools
```

期望输出 `active`。

### 第四步：确认 C 工具链可用

依次执行：

```bash
gcc --version
```

```bash
make --version
```

```bash
gdb --version
```

```bash
git --version
```

期望四条命令各输出一行版本信息。任意一条提示 `command not found`，说明对应的软件包没有装上，按 [`操作手册.md`](操作手册.md) 第十一节处理后再重新验证。

### 第五步：编译并运行 hello.c

进入你建立的实验目录（示例）：

```bash
cd ~/oslab/Lab1
```

确认 `hello.c` 就在这里：

```bash
ls hello.c
```

编译：

```bash
gcc -std=c11 -Wall -Wextra -Werror -g hello.c -o hello
```

编译成功时 `gcc` 不会有任何输出。如果出现 `error:` 或 `warning:`，按提示的行号回到 `hello.c` 修改后重新编译。

运行：

```bash
./hello
```

期望输出两行：第一行是 `Hello, Operating Systems!`，第二行是你填在注释里的**本人学号与姓名**。

> `hello.c` 是本作业的必交文件。文件开头要保留写明本人学号姓名的注释块，整个文件的非空行不少于 10 行。自动审核会在 AI 检查之前先统计非空行数，行数不足会直接判为“文件内容无效”。

### 第六步：验证 VMware Tools 桌面功能

用鼠标拖动 VMware 虚拟机窗口的边缘改变窗口大小，观察 Ubuntu 桌面分辨率是否自动调整。

### 第七步：填写检查结果

| 项目 | 你的填写内容 |
| :--- | :--- |
| VMware Tools 版本 | |
| `open-vm-tools` 是否 active | |
| 桌面分辨率是否能自动调整 | |
| `gcc` 版本 | |
| `make` 版本 | |
| `gdb` 版本 | |
| `git` 版本 | |
| `gcc` 编译 `hello.c` 是否成功 | |
| `./hello` 的运行输出 | |
| 五项组件是否全部验收合格 | |

![工具链与第一个程序](imgs/lab1-toolchain.png)

---

## 环境验收总结

| 验收项目 | 合格标准 | 你的结论 |
| :--- | :--- | :--- |
| VMware 版本 | VMware Workstation Pro 26H1 for Windows | |
| Linux 版本 | Ubuntu 24.04 LTS Desktop amd64，安装介质为教师提供的 24.04.4 | |
| 虚拟机联网 | 具有 IP 和默认路由，IP 联通与 DNS 解析正常 | |
| 国内软件源 | 已换成国内镜像站，`sudo apt update` 成功 | |
| CPU、内存、存储 | 至少 2 核、4GB、40GB，且与宿主机档位匹配 | |
| C 开发工具链 | `gcc`、`make`、`gdb`、`git` 可用，`hello.c` 能编译运行 | |
| VMware Tools | 软件包已安装，`open-vm-tools` 为 active，窗口缩放分辨率自动适配 | |

简要说明你遇到的问题、解决方法，以及当前环境是否可以继续完成后续实验：

> 填写：

---

## 截图要求

- 截图须清晰，菜单和终端文字可读。
- 终端截图应同时显示完整命令和其输出。
- 一张截图可以包含同一任务下的多条命令及其输出，但每条命令和它的输出必须能对应上。
- 截图中应能看到学生自己的虚拟机、本人学号姓名或本人程序的运行结果，不得直接使用他人截图。
- 必须使用电脑自带的截图功能，严禁使用手机拍摄屏幕。
- 所有截图放在 `imgs/` 目录中，文件名与下表一致。

| 截图内容 | 文件名 |
| :--- | :--- |
| VMware Workstation About 页面，能看到完整版本 | `imgs/lab1-vmware-version.png` |
| Ubuntu 当前版本、安装介质版本和 `x86_64` 架构 | `imgs/lab1-ubuntu-version.png` |
| IP、默认路由、IP ping、域名 ping 和 `apt update` 成功 | `imgs/lab1-network.png` |
| `nproc`、`free -h`、`lsblk`、`df -h /` 输出 | `imgs/lab1-resources.png` |
| 工具链版本/状态，以及 `hello.c` 的编译命令与运行结果 | `imgs/lab1-toolchain.png` |

---

## 提交要求

在自己的“学号姓名”文件夹下新建 `Lab1/`，提交填写完整的 `Lab1.md`、你自己编写的 `hello.c` 和全部截图：

```text
学号姓名/
└── Lab1/
    ├── Lab1.md
    ├── hello.c
    └── imgs/
        ├── lab1-vmware-version.png
        ├── lab1-ubuntu-version.png
        ├── lab1-network.png
        ├── lab1-resources.png
        └── lab1-toolchain.png
```

> **注意**：`imgs` 全部小写；`hello.c` 的文件名也必须严格写成小写，不能写成 `Hello.c` 或 `hello.C`。文件夹名和截图文件名区分大小写，必须与上面完全一致，否则图片引用会失效。
>
> **只提交上面列出的文件。** 建议在虚拟机里单独建一个实验目录（如 `~/oslab/Lab1`）写代码、编译，确认无误后再把 `hello.c` 复制进仓库的 `Lab1/` 里。`gcc` 生成的可执行文件 `hello` 没有扩展名、不属于本次作业的提交内容，如果你是在仓库目录里直接编译的，提交前请先把 `hello` 删掉，再用 `git status` 确认变更文件只有上面这 7 个。

---

## 截止时间

**2026 年 10 月 8 日 23:59:59**

按仓库 `README.md` 第 4 节的规则：不晚于 10 月 8 日 23:59:59 创建 PR 并完成最后一次推送不算超时，10 月 9 日 00:00 起新建 PR 或向已有 PR 推送任何修改均算作超时。时间按北京时间计算，并以 GitHub 记录的最后一次推送时间为准。审核未通过的同学请务必在截止前完成修改。
