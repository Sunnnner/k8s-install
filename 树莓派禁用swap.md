- 编辑`/etc/dphys-swapfile`的`CONF_SWAPSIZE`字段，修改swap交换分区。

 

- 首先删除掉swap文件。一般在var目录下，根据实际情况修改：

```bash
sudo swapoff /swapfile
sudo rm /var/swapfile
```

- 然后修改`/etc/dphys-swapfile`（没有vim的可以用nano或vi替代，或者`apt install vim`）

```bash
vim /etc/dphys-swapfile
```

- 如果禁用，将`CONF_SWAPSIZE`修改为0即可。不禁用的话修改为你想修改成的值

- 接着重启你的树莓派。重启后输入free -m，看看swap分区是不是已经成功修改了呢