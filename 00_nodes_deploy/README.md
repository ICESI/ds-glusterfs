### Glusterfs (Inicialización)

En el nodo maestro
```
$ sudo gluster peer probe node1
$ sudo gluster peer probe node2
$ sudo gluster peer probe node3
$ gluster pool list
$ sudo gluster volume create swarm-vols replica 4 node0:/gluster/data node1:/gluster/data node2:/gluster/data node3:/gluster/data force
$ sudo gluster volume set swarm-vols auth.allow 127.0.0.1
$ sudo gluster volume start swarm-vols
```

En todos los nodos
```
$ sudo mount.glusterfs localhost:/swarm-vols /swarm/volumes
```

Para añadir un nuevo servidor

| Command  | Description  |
|---|---|
| gluster peer status | Consulte el estado del cluster |
| gluster peer probe node4 | Adicione el nuevo nodo |
| gluster volume status | Anote el nombre del volumen |
| gluster volume add-brick swarm-vols replica 5 node4:/gluster/data/swarm-vols | TODO: Verificar este comando |

Para remover un nodo del cluster se requiere primero remover sus bricks de los volumenes asociados

| Command  | Description  |
|---|---|
| gluster volume info | Consulte los identificadores de los bricks actuales |
| gluster volume remove-brick swarm-vols replica 1 node1:/gluster/data force | Remueve un brick de un volumen con dos replicas |
| gluster peer detach node1 | Remueve un nodo del cluster |

Eliminar un volumen

| Command  | Description  |
|---|---|
| gluster volume stop swarm-vols | Detenga el volumen |
| gluster volume delete swarm-vols | Elimine el volumen |

### Troubleshoothing

#### Caso 1:
```
$ sudo gluster peer probe node2
unable-to-peer-probe-in-glusterfs-transport-endpoint-is-not-connected
```

#### Solución:
Verifique que el nodo 2 tiene una ip privada asignada (192.168.56.103)
```
$ ip a
3: enp0s8: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 08:00:27:d6:35:09 brd ff:ff:ff:ff:ff:ff

$ vi /etc/network/interfaces
auto enp0s8
iface enp0s8 inet static
      address 192.168.56.103
      netmask 255.255.255.0

$ /etc/init.d/networking restart

$ ip a
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:d6:35:09 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.103/24 brd 192.168.56.255 scope global enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fed6:3509/64 scope link
       valid_lft forever preferred_lft forever
```

### References
* https://docs.gluster.org/en/v3/Administrator%20Guide/Managing%20Volumes/
* https://support.rackspace.com/how-to/recover-from-a-failed-server-in-a-glusterfs-array/
* https://support.rackspace.com/how-to/add-and-remove-glusterfs-servers/
* http://embaby.com/blog/using-glusterfs-docker-swarm-cluster/
* https://docs.gluster.org/en/latest/Quick-Start-Guide/Quickstart/
* http://ask.xmodulo.com/create-mount-xfs-file-system-linux.html
* https://www.cyberciti.biz/faq/linux-how-to-delete-a-partition-with-fdisk-command/
* https://support.rackspace.com/how-to/getting-started-with-glusterfs-considerations-and-installation/
* https://everythingshouldbevirtual.com/virtualization/vagrant-adding-a-second-hard-drive/
