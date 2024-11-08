---
title: "Linux安装代理工具"
description: 
date: 2024-11-08T21:35:31+08:00
image: img.jpg
hidden: false
comments: true
draft: true
tags: ["教程"]
categories: ["教程"]
---

# Linux 安装代理工具

## Ubuntu/Debian

### 方法一：通过软件源安装

#### 添加公钥

~~~shell
wget -qO - https://apt.v2raya.org/key/public-key.asc | sudo tee /etc/apt/keyrings/v2raya.asc
~~~

#### 添加 V2RayA 软件源

~~~shell
echo "deb [signed-by=/etc/apt/keyrings/v2raya.asc] https://apt.v2raya.org/ v2raya main" | sudo tee /etc/apt/sources.list.d/v2raya.list
sudo apt update
~~~

#### 安装 V2rayA

~~~shell
sudo apt install v2raya v2ray
~~~

### 方法二：手动安装 deb 包

~~~shell
sudo apt install /path/download/installer_debian_xxx_vxxx.deb ### 自行替换 deb 包所在的实际路径
~~~

V2Ray / Xray 的 deb 包可以在 [APT 软件源中](https://github.com/v2rayA/v2raya-apt/tree/master/pool/main/) 找到。

### 启动 v2rayA / 设置 v2rayA 自动启动

> 从 1.5 版开始将不再默认为用户启动 v2rayA 及设置开机自动。

- 启动 v2rayA

  ```bash
  sudo systemctl start v2raya.service
  ```

- 设置开机自动启动

  ```bash
  sudo systemctl enable v2raya.service
  ```



## macos

### 安装

添加 v2rayA 的 Tap：

```bash
brew tap v2raya/v2raya
```

安装 v2rayA:

```bash
brew install v2raya/v2raya/v2raya
```

`v2ray` 设置为了 v2rayA 的依赖包，如此，v2rayA 将正确运行。

#### 运行

安装完成之后可以在终端通过 `v2raya --lite` 命令运行，也可以启动服务：

```bash
brew services start v2raya
```

### 手动安装

#### 创建目录

这些目录可能已经存在，创建之前请注意检查。

二进制所在目录：

```bash
sudo mkdir /usr/local/bin/
```

数据文件所在目录：

```bash
sudo mkdir -p /usr/local/share/v2ray/
```

服务文件所在目录：

```bash
mkdir ~/Library/LaunchAgents/
```

#### 下载 v2rayA

从 [GitHub Releases](https://github.com/v2rayA/v2rayA/releases) 或 GitHub Action 下载适用于 macOS 的二进制文件，然后重命名为 `v2raya`，并将其保存到 `/usr/local/bin/`。

示例：

x86_64:

```bash
sudo curl -L https://github.com/v2rayA/v2rayA/releases/download/v1.5.7/v2raya_darwin_x64_1.5.7 -o /usr/local/bin/v2raya
```

arm64:

```bash
sudo curl -L https://github.com/v2rayA/v2rayA/releases/download/v1.5.7/v2raya_darwin_arm64_1.5.7 -o /usr/local/bin/v2raya
```

#### 下载 V2Ray 核心 / Xray 核心

> 安装 V2Ray：https://www.v2fly.org/guide/install.html 安装 Xray：https://xtls.github.io/document/install.html

解压压缩包后将其中的文件移动到对应目录：

```bash
sudo mv v2ray /usr/local/bin/
sudo mv *dat /usr/local/share/v2ray/
```

#### 给予权限

给予 v2rayA 与 v2ray 可执行权限：

```bash
sudo chmod 755 /usr/local/bin/v2raya
sudo chmod 755 /usr/local/bin/v2ray
sudo chmod 755 /usr/local/bin/v2ctl
```

如果遇到 macOS 的安全限制，那么需要运行以下命令：

```bash
sudo xattr -d -r com.apple.quarantine  /usr/local/bin/*
```

#### 建立服务文件

新建服务文件并保存到 `~/Library/LaunchAgents/`

示例：

```bash
nano ~/Library/LaunchAgents/org.v2raya.v2raya.plist
```

内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
      <key>EnvironmentVariables</key>
      <dict>
            <key>V2RAYA_LOG_FILE</key>
            <string>/tmp/v2raya.log</string>
            <key>V2RAYA_V2RAY_BIN</key>
            <string>/usr/local/bin/v2ray</string>
      </dict>
      <key>KeepAlive</key>
      <true/>
      <key>Label</key>
      <string>org.v2raya.v2raya</string>
      <key>ProgramArguments</key>
      <array>
            <string>/usr/local/bin/v2raya</string>
            <string>--lite</string>
      </array>
      <key>RunAtLoad</key>
      <true/>
</dict>
</plist>
```

#### 运行

```bash
launchctl load ~/Library/LaunchAgents/org.v2raya.v2raya.plist
```

如果要关掉 v2rayA 服务，将上述命令从 `load` 替换为 `unload` 即可。可以通过 Web 前端查看日志。

