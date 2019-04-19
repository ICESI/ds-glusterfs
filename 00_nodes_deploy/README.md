### Glusterfs (Inicialización)

En el nodo maestro
```
sudo gluster peer probe node1
sudo gluster peer probe node2
sudo gluster peer probe node3
gluster pool list
sudo gluster volume create swarm-vols replica 4 node0:/gluster/data node1:/gluster/data node2:/gluster/data node3:/gluster/data force
sudo gluster volume set swarm-vols auth.allow 127.0.0.1
sudo gluster volume start swarm-vols
```

En todos los nodos
```
sudo mount.glusterfs localhost:/swarm-vols /swarm/volumes
```

### References
* http://embaby.com/blog/using-glusterfs-docker-swarm-cluster/
* https://docs.gluster.org/en/latest/Quick-Start-Guide/Quickstart/
* http://ask.xmodulo.com/create-mount-xfs-file-system-linux.html
* https://www.cyberciti.biz/faq/linux-how-to-delete-a-partition-with-fdisk-command/
* https://support.rackspace.com/how-to/getting-started-with-glusterfs-considerations-and-installation/
* https://everythingshouldbevirtual.com/virtualization/vagrant-adding-a-second-hard-drive/
