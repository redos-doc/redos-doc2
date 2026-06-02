hostname hq-rtr.au-team.irpo

object-group network LOCAL_ADDRESS
  ip address-range 192.168.100.1-192.168.100.15
  ip address-range 192.168.200.1-192.168.200.15
exit
object-group network PUBLIC_ADDRESS
  ip address-range 172.16.1.3-172.16.1.7
exit

syslog max-files 3
syslog file-size 512
syslog file tmpsys:syslog/default
  severity info
exit

username admin
  password encrypted $6$SB.XW2Zri6EMX9tL$twnGI/osrPPI6VUyRDDPWhHipNCWVv0W.y2peNmnfheDGwRlNDIJCr.xQo9G/PXfT7cezLXvMrsGUCTT0Zgji.
exit
username net_admin
  password encrypted $6$UAmFA3JptYYZPWdE$/Av69Lj0IHPMyGt0uR2JFqu4g7u5uKHHAb5P3GVwzuTqWYTqe9/3PK4GzkSTzJzXyXunc9rtoe52Hofwbd6at1
  privilege 15
exit

domain lookup enable

router ospf 10
  area 1.1.1.1
    network 10.10.10.0/28
    network 172.16.1.0/28
    network 192.168.100.0/27
    network 192.168.200.0/27
    enable
  exit
  enable
exit

interface gigabitethernet 1/0/1
  ip firewall disable
  ip address 172.16.1.2/28
  ip nat proxy-arp PUBLIC_ADDRESS
exit
interface gigabitethernet 1/0/2
  ip firewall disable
  ip address 192.168.100.1/27
exit
interface gigabitethernet 1/0/3
  ip firewall disable
  ip dhcp server address 192.168.200.1
  ip address 192.168.200.1/27
exit

tunnel gre 1
  ttl 225
  ip firewall disable
  local address 172.16.1.2
  remote address 172.16.2.2
  ip address 10.10.10.1/28
  ip ospf instance 10
  ip ospf area 1.1.1.1
  ip ospf
  enable
exit

security passwords default-expired

nat source
  pool TRANSLATE_ADDRESS
    ip address-range 172.16.1.3-172.16.1.7
  exit
  ruleset SNAT
    to interface gigabitethernet 1/0/1
    rule 1
      match source-address object-group LOCAL_ADDRESS
      action source-nat pool TRANSLATE_ADDRESS
    exit
  exit
exit

ip dhcp-server
ip dhcp-server pool HQ-CLI
  network 192.168.200.0/27
  domain-name au-team.irpo
  address-range 192.168.200.2-192.168.200.15
  default-router 192.168.200.1
  dns-server 192.168.100.2
exit

ip route 0.0.0.0/0 172.16.1.1
ip route 172.16.2.0/28 172.16.1.1

ip ssh server

ntp enable
ntp broadcast-client enable

licence-manager
  host address elm.eltex-co.ru
exit




BR-RTR
hostname br-rtr.au-team.irpo

syslog max-files 3
syslog file-size 512
syslog file tmpsys:syslog/default
  severity info
exit

username admin
  password encrypted $6$/W8AXkko.OlHGIyL$VSKoShTuCcktFQ1Uk5BsqtuccNxdGY9M5FEbEULro1D44vivbMfjmNOtJyWWvc7yPZp6B3Sn.snw3EJv5Vjuh1
exit
username net_admin
  password encrypted $6$0A7G55Ry0TkaqLyP$IwyH3hBxUMVH5RUCmft3Wvd5bK4QwJ7HKjkpIXqW6u9NV4TNwjDSpTY5HQOrD4zOrKhH4Z52DRoQwsBXPDRP7/
  privilege 15
exit

domain lookup enable

router ospf 10
  area 1.1.1.1
    network 172.16.2.0/28
    network 10.10.10.0/28
    enable
  exit
  enable
exit

interface gigabitethernet 1/0/1
  ip firewall disable
  ip address 172.16.2.2/28
exit

tunnel gre 1
  ttl 225
  ip firewall disable
  local address 172.16.2.2
  remote address 172.16.1.2
  ip address 10.10.10.2/28
  ip ospf instance 10
  ip ospf area 1.1.1.1
  ip ospf
  enable
exit

security passwords default-expired

ip route 0.0.0.0/0 172.16.2.1
ip route 172.16.1.0/28 172.16.2.1
ip route 192.168.100.0/27 172.16.2.1
ip route 192.168.200.0/27 172.16.2.1

ip ssh server

ntp enable
ntp broadcast-client enable

licence-manager
  host address elm.eltex-co.ru
exit
