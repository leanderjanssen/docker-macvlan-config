# Docker MACVLAN example
---

## 1 - Configure vlan interface using networkd

### Create vlan interface using tag 10
`# /etc/systemd/network/25-vlan10.netdev`
```
[NetDev]
Name=vlan10
Kind=vlan

[VLAN]
Id=10
```

### Associate vlan interface with physical interface
`# /etc/systemd/network/40-eth1.network`
```
[Match]
Name=eth1

[Network]
VLAN=vlan10
```

## 2 - Docker network setup
### Create network using the macvlan driver
```
docker network create \
  --driver macvlan \
  --subnet=192.168.10.0/24 \
  --gateway=192.168.10.1 \
  --ip-range=192.168.10.128/25 \
  -o parent=vlan10 \
  vlan10
```

### Create container and attach it to this network
`docker run --net=vlan10 -d nginx`
