# Running OpenWrt on X86

This is a OpenWrt image running on X86 Linux Docker. 

To use this image, you need to install docker first.

Then you can import this image. For example, we name this machine `s2s`

```
docker import /opt/openwrt-x86-64-generic-rootfs.tar.gz s2s
```

To start the OpenWrt system and bridge it to your Linux Host, using the following.
`12345` is the port on Linux Host and `22` is the port of OpenWrt ssh port.

```
docker run -d -p 51830:51830/udp -p 12345:22 --privileged --name s2s s2s /sbin/init
```

To disconnect network

```
docker network disconnect bridge s2s
```

To start the network
```
docker network connect bridge s2s
```

Now you can ssh to the OpenWrt system on Linux Host by access the port `12345`
```
ssh -p 12345 root@127.0.0.1
```

To Set up a Site 2 Site connect to GL.iNet GoodCloud, you need to get configuration from GL.iNet and put on the OpenWrt.
The content of /etc/config/glconfig is

```
config service 'info'
	option ddns 'xxxx'
	option mac 'xx:xx:xx:xx:xx:xx'
	option sn 'xxxxxxxxxx'

config service 'cloud'
	option enable '1'
	option check_status '1'
	option serverzone 'gslb-jp.goodcloud.xyz'
```

