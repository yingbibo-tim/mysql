## 阿里云配置ipv6 centos7 主要用于ios APP 审核

step1 :vi /etc/sysctl.conf

<code>
<p>net.ipv6.conf.all.disable_ipv6 = 0</p>
<p>net.ipv6.conf.default.disable_ipv6 = 0</p>
<p>net.ipv6.conf.lo.disable_ipv6 = 0</p>
</code>

sep2: sysctl -p

step3 :注册隧道

https://www.tunnelbroker.net/

  ** Create Regular Tunnel
  
  ** Example Configurations -- > Linux-route2
  eg:
  
step4:

<p> modprobe ipv6 </p>
<p> ip tunnel add he-ipv6 mode sit remote 216.218.221.6 local ** 内网IP 记住内网IP ** ttl 255 </p>
<p> ip link set he-ipv6 up </p>
<p> ip addr add 2001:470:18:175d::2/64 dev he-ipv6 </p>
<p> ip route add ::/0 dev he-ipv6 </p>
<p> ip -f inet6 addr </p>

ping ping6 -c 5 ipv6.google.com 能ping通说明配置OK了


如果不小心 step4 的时候弄错了.....
 
 <code>
 
  ip addr del 2001:470:18:1758::2/64 dev he-ipv6
  
  service network restart
  
  重复step4
 
 </code>
  
  
