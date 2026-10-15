# Linux 校招常用命令速查

> 命令格式：`命令 [选项] [参数]`
> 校招重点：文件目录、权限管理、文本处理、进程网络、压缩查找、管道重定向。

---

## 一、帮助与基础

| 命令 | 作用 |
| --- | --- |
| `man ls` | 查看命令详细手册（按 q 退出） |
| `ls --help` | 查看简要帮助 |
| `history` | 查看历史命令 |
| `!!` | 执行上一条命令 |
| `!$` | 引用上一条命令的最后一个参数 |
| `alias ll='ls -l'` | 定义命令别名 |
| `clear` | 清屏（Ctrl + L 同样效果） |
| `Tab` | 自动补全路径/命令 |

---

## 二、文件与目录

| 命令 | 作用 |
| --- | --- |
| `pwd` | 显示当前所在目录 |
| `cd /etc` | 切换到指定目录 |
| `cd -` | 回到上一次所在目录 |
| `cd ~` | 回到当前用户家目录 |
| `ls -alh` | 列出全部文件（含隐藏）、详情、人类可读大小 |
| `ls -R` | 递归列出子目录 |
| `mkdir -p a/b/c` | 递归创建多级目录 |
| `rmdir dir` | 删除空目录 |
| `rm -rf dir` | 强制递归删除（**慎用，不可恢复**） |
| `cp -r dir1 dir2` | 递归复制目录 |
| `cp -a src dst` | 归档复制，保留权限/时间 |
| `mv old new` | 移动或重命名 |
| `touch file` | 创建空文件 / 更新时间戳 |
| `file file` | 查看文件类型 |
| `stat file` | 查看文件详细元信息（inode、时间等） |
| `tree` | 以树形结构展示目录 |

---

## 三、查找命令

| 命令 | 作用 |
| --- | --- |
| `find . -name "*.log"` | 当前目录按名称查找 |
| `find / -type f -size +100M` | 查找大于 100M 的文件 |
| `find . -mtime -1` | 查找最近 1 天内修改的文件 |
| `find . -name "*.tmp" -exec rm {} \;` | 查找后批量删除 |
| `locate file` | 从数据库快速查找（需 `updatedb` 更新） |
| `which java` | 查找可执行命令的路径 |
| `whereis nginx` | 查找程序的二进制、源码、手册位置 |
| `grep -rn "error" /var/log` | 递归查找文件内容并显示行号 |

> `find` 是**找文件**，`grep` 是**找文件内容**。

---

## 四、查看文件

| 命令 | 作用 |
| --- | --- |
| `cat file` | 正向查看全文 |
| `tac file` | 反向查看全文 |
| `more file` | 分页查看（空格下翻，不能上翻） |
| `less file` | 分页查看（可上下翻，推荐） |
| `head -n 20 file` | 查看前 20 行 |
| `tail -n 20 file` | 查看后 20 行 |
| `tail -f app.log` | 实时追踪日志 |
| `nl file` | 显示行号查看 |
| `wc -l file` | 统计行数 |
| `wc -w file` | 统计单词数 |
| `wc -c file` | 统计字节数 |

---

## 五、文本处理（三剑客：grep / sed / awk）

### grep —— 过滤查找

```bash
grep "error" file          # 查找包含 error 的行
grep -i "error" file       # 忽略大小写
grep -v "debug" file       # 反向过滤（不包含）
grep -n "error" file       # 显示行号
grep -c "error" file       # 统计匹配行数
grep -rn "TODO" .          # 递归查找当前目录
grep -E "error|warn" file  # 正则匹配（等价 egrep）
```

### sed —— 流编辑/替换

```bash
sed -i 's/old/new/g' file     # 全局替换并写回文件
sed -n '1,10p' file           # 只打印第 1-10 行
sed '/^#/d' file              # 删除以 # 开头的行
sed -n '/error/p' file        # 打印匹配行
```

### awk —— 按列处理

```bash
awk '{print $1}' file                # 打印第 1 列
awk -F: '{print $1,$3}' /etc/passwd  # 以 : 分隔，打印第 1、3 列
awk '$3>100 {print}' file            # 第 3 列大于 100 才打印
awk '{sum+=$1} END{print sum}' file  # 对第 1 列求和
```

### 其他常用

| 命令 | 作用 |
| --- | --- |
| `cut -d: -f1 file` | 以分隔符取列 |
| `sort -nr file` | 按数字逆序排序（`-k2` 指定列） |
| `sort file \| uniq -c` | 排序后去重并计数（uniq 前必须先 sort） |
| `tr 'a-z' 'A-Z'` | 字符转换（小写转大写） |
| `paste f1 f2` | 合并文件行 |
| `diff f1 f2` | 比较两个文件差异 |

**统计访问日志 Top IP：**

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head
```

---

## 六、权限管理

### 权限数字

| 权限 | 数字 |
| --- | --- |
| `r`（读） | 4 |
| `w`（写） | 2 |
| `x`（执行） | 1 |

```bash
chmod 755 file       # rwxr-xr-x
chmod 644 file       # rw-r--r--
chmod +x script.sh   # 增加执行权限
chmod u+x file       # 给所有者加执行权限
chmod -R 755 dir     # 递归修改目录权限
chown user file      # 修改所有者
chown -R user:group dir  # 递归修改所有者和属组
chgrp group file     # 修改属组
umask                # 查看默认权限掩码（默认 022）
```

---

## 七、用户与组管理

| 命令 | 作用 |
| --- | --- |
| `useradd -m user` | 创建用户并建立家目录 |
| `passwd user` | 设置密码 |
| `userdel -r user` | 删除用户及家目录 |
| `usermod -aG group user` | 将用户加入附加组 |
| `groupadd group` | 创建组 |
| `id user` | 查看用户 UID/GID |
| `whoami` | 当前用户名 |
| `su - user` | 切换用户（`-` 加载环境变量） |
| `sudo command` | 以管理员权限执行 |
| `who` / `w` | 查看当前登录用户 |

---

## 八、进程管理

| 命令 | 作用 |
| --- | --- |
| `ps -ef` | 查看所有进程（标准格式） |
| `ps aux` | 查看所有进程（BSD 格式，含 CPU/内存） |
| `ps -ef \| grep java` | 查找 Java 进程 |
| `top` | 动态查看资源占用（P 按 CPU 排序，M 按内存） |
| `htop` | top 的增强版 |
| `kill PID` | 正常结束进程 |
| `kill -9 PID` | 强制杀死进程 |
| `killall name` | 按进程名杀死 |
| `pkill -f name` | 按名称模糊匹配杀死 |
| `jobs` | 查看后台任务 |
| `bg %1` / `fg %1` | 任务转后台/前台 |
| `Ctrl + Z` | 暂停当前进程 |
| `Ctrl + C` | 终止当前进程 |

**后台运行（关闭终端也不退出）：**

```bash
nohup ./run.sh > out.log 2>&1 &
```

---

## 九、系统信息

| 命令 | 作用 |
| --- | --- |
| `uname -a` | 内核及系统信息 |
| `hostname` | 主机名 |
| `uptime` | 运行时间与负载 |
| `free -h` | 内存使用情况 |
| `df -h` | 磁盘分区使用情况 |
| `du -sh *` | 当前目录各项大小 |
| `date` | 当前时间 |
| `cal` | 日历 |
| `last` | 登录记录 |
| `lscpu` | CPU 信息 |
| `lsblk` | 块设备/磁盘信息 |

---

## 十、网络命令

| 命令 | 作用 |
| --- | --- |
| `ping -c 4 host` | 测试连通性（发 4 个包） |
| `ip addr` | 查看 IP 地址（新版） |
| `ifconfig` | 查看 IP 地址（旧版） |
| `ip route` | 查看路由表 |
| `ss -tunlp` | 查看端口监听（推荐） |
| `netstat -tunlp` | 查看端口监听（旧版） |
| `lsof -i:8080` | 查看 8080 端口占用 |
| `curl -I URL` | 查看响应头 |
| `curl -X POST URL` | 发送 POST 请求 |
| `wget URL` | 下载文件 |
| `ssh user@host` | 远程登录 |
| `scp file user@host:/path` | 远程复制文件 |
| `scp -r dir user@host:/path` | 远程复制目录 |
| `rsync -avz src dst` | 增量同步 |
| `telnet host port` | 测试端口连通 |
| `nc -zv host port` | 测试端口是否开放 |

---

## 十一、压缩与打包

### tar（参数含义：c 创建 / x 解压 / z gzip / v 显示 / f 文件）

```bash
tar -czvf a.tar.gz dir      # 压缩为 .tar.gz
tar -xzvf a.tar.gz          # 解压
tar -xzvf a.tar.gz -C /tmp  # 解压到指定目录
tar -tzvf a.tar.gz          # 查看压缩包内容
```

### 其他

| 命令 | 作用 |
| --- | --- |
| `gzip file` / `gunzip file.gz` | 压缩/解压单个文件（不保留原文件） |
| `zip -r a.zip dir` | 压缩为 zip |
| `unzip a.zip -d /tmp` | 解压 zip 到指定目录 |

---

## 十二、重定向与管道

| 符号 | 作用 |
| --- | --- |
| `cmd > file` | 标准输出**覆盖**写入 |
| `cmd >> file` | 标准输出**追加**写入 |
| `cmd < file` | 从文件读入作为输入 |
| `cmd 2> err.log` | 错误输出到文件 |
| `cmd > file 2>&1` | 标准输出和错误输出都写入文件 |
| `cmd1 \| cmd2` | 管道：前一个输出作为后一个输入 |
| `cmd \| tee file` | 同时输出到屏幕和文件 |
| `cmd \| xargs` | 将输出转为命令参数 |

---

## 十三、环境变量

| 命令 | 作用 |
| --- | --- |
| `env` | 查看所有环境变量 |
| `export PATH=$PATH:/new/path` | 临时添加环境变量 |
| `echo $PATH` | 查看 PATH 变量 |
| `source ~/.bashrc` | 重新加载配置使其生效 |

常见配置文件：`/etc/profile`（全局）、`~/.bashrc`（当前用户）。

---

## 十四、包管理、服务与定时任务

| 命令 | 作用 |
| --- | --- |
| `apt update && apt install pkg` | Debian/Ubuntu 安装软件 |
| `yum install pkg` / `dnf install pkg` | CentOS/RHEL 安装 |
| `rpm -ivh pkg.rpm` / `dpkg -i pkg.deb` | 安装本地包 |
| `systemctl start nginx` | 启动服务 |
| `systemctl stop nginx` | 停止服务 |
| `systemctl status nginx` | 查看状态 |
| `systemctl enable nginx` | 开机自启 |
| `journalctl -u nginx -f` | 查看服务日志 |
| `crontab -e` | 编辑定时任务 |
| `crontab -l` | 查看定时任务 |

---

## 十五、高频场景速查

```bash
# 1. 查看端口占用
ss -tunlp | grep :8080
lsof -i:8080

# 2. 查找进程
ps -ef | grep nginx

# 3. 强制杀死进程
kill -9 PID

# 4. 实时查看日志
tail -f app.log

# 5. 查找大文件（>100M）
find / -type f -size +100M

# 6. 批量替换文件内容
sed -i 's/old/new/g' file

# 7. 统计日志中出现最多的 IP
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head

# 8. 统计文件行数
wc -l file

# 9. 查找最近 7 天的 log 文件并删除
find . -name "*.log" -mtime +7 -exec rm -f {} \;

# 10. 后台运行程序
nohup ./app > app.log 2>&1 &
```

---

## 十六、易错点 / 面试对比

1. **软链接 vs 硬链接**
   - 软链接：`ln -s 源文件 链接`，类似快捷方式，可跨文件系统/目录，删除源文件后链接失效。
   - 硬链接：`ln 源文件 链接`，与源文件共享同一个 inode，不能跨文件系统、不建议链接目录，删除源文件后硬链接仍可访问。

2. **find vs grep**：find 找文件，grep 找文件内容。

3. **`>` vs `>>`**：`>` 覆盖，`>>` 追加。

4. **`ps aux` vs `ps -ef`**：都能列出全部进程，aux 为 BSD 格式（带 CPU/内存百分比），-ef 为标准格式（带 PPID）。

5. **`which` vs `whereis` vs `locate`**：which 找可执行文件路径；whereis 找二进制/源码/手册；locate 从数据库快速查找。

6. **权限含义**：755 = rwxr-xr-x（所有者可写可执行，其他人只读执行）；644 = rw-r--r--。

7. **tar 参数**：c 打包、x 解包、z gzip、v 显示过程、f 指定文件名（f 后必须紧跟文件名）。

8. **grep / sed / awk**：grep 负责过滤，sed 负责替换编辑，awk 负责按列分析。

---

## 复习建议

- 笔试常考**命令作用、常用选项、权限数字**；面试常考**实际场景排障**。
- 优先掌握：文件操作、权限、grep/sed/awk、进程与端口、tar 压缩、管道重定向。
- 建议在虚拟机里逐条敲一遍，结合场景记忆，比死记硬背效果更好。
