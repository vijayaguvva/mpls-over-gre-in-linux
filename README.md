# mpls-over-gre-in-linux
Steps to configure mpls over gre tunnel in linux

Lets assume two systems A and B are connected back to back
and are assigned 192.168.1.1 and 192.168.1.2.
In this example MPLS lable 100 is used for traffic from A to B
and 200 is used for traffic from B to A.

Make sure kernel is compiled with IPIP, MPLS support and iproute2 uitls
has support to configure MPLS and ipip tunnels.

On system A:

    $ ifconfig eth0 192.168.1.1/24
    $ ip tun add gre-tun mode gre remote 192.168.1.2 local 192.168.1.1
    $ ifconfig gre-tun 10.1.1.1/24
    $ ip route change 10.1.1.0/24 encap mpls 100 dev gre-tun
    $ ip -f mpls route add 200 dev lo
    $ sysctl -w net.mpls.conf.gre-tun.input=1

On System B:

    $ ifconfig eth0 192.168.1.2/24
    $ ip tun add gre-tun mode gre remote 192.168.1.1 local 192.168.1.2
    $ ifconfig gre-tun 10.1.1.2/24
    $ ip route change 10.1.1.0/24 encap mpls 200 dev gre-tun
    $ ip -f mpls route add 100 dev lo
    $ sysctl -w net.mpls.conf.gre-tun.input=1
