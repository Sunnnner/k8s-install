- [ERROR SystemVerification]: missing required cgroups: memory

- 解决方法
```bash
sudo nano /boot/cmdline.txt
# 输入以下内容
console=serial0,115200 console=tty1 root=PARTUUID=eee58bbc-02 rootfstype=ext4 elevator=deadline cgroup_memory=1 cgroup_enable=memory fsck.repair=yes rootwait quiet splash plymouth.ignore-serial-consoles
```

- 使用`journalctl -f -u kubelet`查看k8s日志
- 查看pod错误信息` kubectl describe pod -n kube-system pod_name`
