# Linux

## 命令

### 关机、重启及注销

* `shutdown -h now `即刻关机
* `shutdown -h 10 `10分钟后关机
* `shutdown -h 11:00 `11：00关机
* `shutdown -h +10` 预定时间关机（10分钟后）
* `shutdown -c` 取消指定时间关机
* `shutdown -r now` 重启
* `shutdown -r 10` 10分钟之后重启
* `shutdown -r 11:00` 定时重启
* `reboot` 重启
* `init 6` 重启
* `init 0 `⽴刻关机
* `telinit 0` 关机
* `poweroff` ⽴刻关机
* `halt` 关机
* `sync buff`数据同步到磁盘
* `logout` 退出登录Shell

### 常⻅系统服务命令

* `chkconfig --list `列出系统服务
* `service <服务名> status `查看某个服务
* `service <服务名> start `启动某个服务
* `service <服务名> stop `终⽌某个服务
* `service <服务名> restart `重启某个服务
* `systemctl status <服务名> `查看某个服务
* `systemctl start <服务名> `启动某个服务
* `systemctl stop <服务名> `终⽌某个服务
* `systemctl restart <服务名> `重启某个服务
* `systemctl enable <服务名> `开启⾃启动
* `systemctl disable <服务名> `关闭⾃启动