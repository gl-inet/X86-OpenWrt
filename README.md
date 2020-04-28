# Running OpenWrt on X86

This is a OpenWrt image running on X86 Linux Docker,we did the demo using ubuntu.

If you are on `ubuntu system`, please follow the instructions below to install the wireguard service on the host.Other types of servers require you to query related commands yourself.

```
$ sudo add-apt-repository ppa:wireguard/wireguard
$ sudo apt-get update
$ sudo apt-get install wireguard docker.io

```
Get the image, and move to directory /opt

```
$ git clone https://github.com/gl-inet/X86-OpenWrt.git
$ cd X86-OpenWrt
$ mv openwrt-x86-64-generic-rootfs.tar.gz /opt

```

Then you can import this image. For example, we name this machine `s2s`

```
docker import /opt/openwrt-x86-64-generic-rootfs.tar.gz s2s
```

open the port 51830 on x86 Linux(For example, Alibaba Cloud, you need to enter the server instance list, find the instance you want to add a port, and click more on the back to enter the security group configuration option to configure the port.)
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

Modify the configuration of /etc/config/glconfig (create the glconfig file if not, note that cloud selection should be based on actual needs)

gslb-cn.goodcloud.xyz/gslb.gl-inet.cn;There are two options for Chinese users with the chinese server, and "gslb.gl-inet.cn" is recommended.

gslb-jp.goodcloud.xyz;It's for Global user with Japan server.

gslb-eu.goodcloud.xyz;It's for Europe user with Europe server.

gslb-us.goodcloud.xyz;It's for America user with America server.

The ddns,mac,sn need to be obtained from the technician,you can send email to xinshouyong@gl-inet.com for information

The glconfig configuration is as follows

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


Set firmware version information

```
 echo "3.027" > /etc/glversion
```

Restart the `MQTT` service
```
 /etc/init.d/gl_mqtt restart
```

Register device information in the corresponding cloud platform

After the configuration is successful, the site to site view of the router is displayed as a solid line on the cloud platform.
