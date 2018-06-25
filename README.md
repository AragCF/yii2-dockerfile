# Use it

Create alias to host machine

## MacOs

```
sudo ifconfig en0 alias 10.254.254.254 255.255.255.0
```

## Linux

```
sudo ip addr add 10.254.254.254/24 brd + dev eth0 label eth0:1
```

##### Other

* jenkins - https://github.com/jenkinsci/docker