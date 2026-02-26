+++
date = '2025-11-08T20:47:58+08:00'
draft = false
title = 'wsl ubuntu reverse-search 按tab卡住'
+++

在 wsl ubuntu 里使用 reverse-search (Ctrl + r) 时，按 tab 键会卡住，无法继续输入命令，卡死时间长达1分钟， terminal直接被冻结，很久之前便遇到了，但之后基本都是服务器上使用linux，最近用wsl又遇到这个问题，几次下来给我整红温了。

有说法是 wsl的 交换区太少导致的， 调整后仍然没用。 
![tab卡住时的资源情况](htop.png)
在tab卡住时重新开一个终端，用htop查看资源使用情况， 就看到一个bash 进程占用了非常多cpu， 检查它发现一直再用syscalls去访问/mnt/c的文件目录，每个文件都给我扫了一遍， 每次都要扫一分钟，问了gpt，说是wsl2会在启动时自动在path里添加windows的目录， 导致bash在reverse-search时会去扫描这些目录下的文件，而wsl是通过网络文件系统系统去访问windows的文件的，速度非常慢 。

解决很简单，阻止 WSL 自动把 Windows 的 PATH 注入到 Linux 的 PATH 里。

在 /etc/wsl.conf 中加入：
```
[interop]
appendWindowsPath = false
```

然后重启 wsl 即可。
