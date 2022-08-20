---
title: ubuntu下u盘变只读（转）
date: 2015-09-10 16:09:44
tags:
- ubuntu
categories:
- [学习, DevOPS, Ubuntu]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 查看挂在格式是否正常：
```
$ mount
/dev/sda1 on / type ext4 (rw,errors=remount-ro)
proc on /proc type proc (rw,noexec,nosuid,nodev)
sysfs on /sys type sysfs (rw,noexec,nosuid,nodev)
none on /sys/fs/cgroup type tmpfs (rw)
none on /sys/fs/fuse/connections type fusectl (rw)
none on /sys/kernel/debug type debugfs (rw)
none on /sys/kernel/security type securityfs (rw)
udev on /dev type devtmpfs (rw,mode=0755)
devpts on /dev/pts type devpts (rw,noexec,nosuid,gid=5,mode=0620)
tmpfs on /run type tmpfs (rw,noexec,nosuid,size=10%,mode=0755)
none on /run/lock type tmpfs (rw,noexec,nosuid,nodev,size=5242880)
none on /run/shm type tmpfs (rw,nosuid,nodev)
none on /run/user type tmpfs (rw,noexec,nosuid,nodev,size=104857600,mode=0755)
none on /sys/fs/pstore type pstore (rw)
systemd on /sys/fs/cgroup/systemd type cgroup (rw,noexec,nosuid,nodev,none,name=systemd)
gvfsd-fuse on /run/user/1000/gvfs type fuse.gvfsd-fuse (rw,nosuid,nodev,user=jalen_chu)
gvfsd-fuse on /home/jalen_chu/.gvfs type fuse.gvfsd-fuse (rw,nosuid,nodev)
/dev/sdb1 on /media/jalen_chu/5426-3BA3 type vfat (rw,nosuid,nodev,uid=1000,gid=1000,shortname=mixed,dmask=0077,utf8=1,showexec,flush,uhelper=udisks2)
```
# 查看属性

$ sudo mount -o remount.rw /dev/sdb1

mount：/dev/sdb1 已挂载或 /media/jalen_chu/5426-3BA3 忙
mount：根据 mtab，/dev/sdb1 已挂载于 /media/jalen_chu/5426-3BA3

# fsck检查u盘数据

$ sudo umount /media/jalen_chu/5426-3BA3

$ sudo fsck /dev/sdb1

fsck，来自 util-linux 2.20.1
fsck.fat 3.0.26 (2014-03-07)
0x41: Dirty bit is set. Fs was not properly unmounted and some data may be corrupt.

以上是查看原因

恢复方法：

1.$ tail -f /var/log/syslog
```
Sep 10 16:26:38 jalen kernel: [26474.564016] sd 7:0:0:0: [sdb] Assuming drive cache: write through
Sep 10 16:26:38 jalen kernel: [26474.588813]  sdb: sdb1
Sep 10 16:26:38 jalen kernel: [26474.591759] sd 7:0:0:0: [sdb] Attached SCSI removable disk
Sep 10 16:26:38 jalen udisksd[1923]: The string `������' is not valid UTF-8. Invalid characters begins at `������'
Sep 10 16:26:38 jalen kernel: [26474.860997] FAT-fs (sdb1): Volume was not properly unmounted. Some data may be corrupt. Please run fsck.
Sep 10 16:26:38 jalen udisksd[1923]: The string `������' is not valid UTF-8. Invalid characters begins at `������'
Sep 10 16:26:38 jalen udisksd[1923]: Mounted /dev/sdb1 at /media/jalen_chu/5426-3BA3 on behalf of uid 1000
Sep 10 16:26:38 jalen dbus[630]: [system] Activating service name='org.freedesktop.hostname1' (using servicehelper)
Sep 10 16:26:38 jalen kernel: [26474.977694] systemd-hostnamed[7896]: Warning: nss-myhostname is not installed. Changing the local hostname might make it unresolveable. Please install nss-myhostname!
Sep 10 16:26:38 jalen dbus[630]: [system] Successfully activated service 'org.freedesktop.hostname1'
Sep 10 16:29:19 jalen udisksd[1923]: The string `������' is not valid UTF-8. Invalid characters begins at `������'
Sep 10 16:29:19 jalen udisksd[1923]: Cleaning up mount point /media/jalen_chu/5426-3BA3 (device 8:17 is not mounted)
Sep 10 16:29:19 jalen udisksd[1923]: Unmounted /dev/sdb1 on behalf of uid 1000
Sep 10 16:29:19 jalen kernel: [26635.889827] sdb: detected capacity change from 7803174912 to 0
Sep 10 16:29:22 jalen kernel: [26639.238370] usb 1-1.2: USB disconnect, device number 5
```
2.插入u盘

3.$ umount /media/5426-3BA3

umount：/media/5426-3BA3 未挂载(从 mtab 得知)

4.$ sudo dosfsck -v -a /dev/sdb4

fsck.fat 3.0.26 (2014-03-07)

fsck.fat 3.0.26 (2014-03-07)

open: 没有那个文件或目录

总结：从网上找的，莫名其妙就好了...
