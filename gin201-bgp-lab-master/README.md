# Lab BGP

In this lab you will play with BGP. Download the pdf of the tasks [here](https://partage.imt.fr/index.php/s/YA9YNBrXz4AtXX2).

## Objectives of the Lab

In this lab you will through practice: 
- The internet routing architecture: BGP, Peering/Transit agreements, Tier1/2/3. 
- BGP policy routing (e.g. local pref, hot potato, tie break) and how it is configured with BGP. 
- Internet Valley Free Routing and how it can be enforced in practice. 
- Examples of what could go wrong: De-peering, Prefix Hijacking.

### Run the Lab

Open a terminal and run

``` sudo kathara lstart ```

A new network will be created. Connect to router AS1 with : 

``` kathara connect AS1 ```


## What to add to modify the lab

For each router we have two things:

- File: r.startup
- Directory: r/etc/quagga

### r.startup

Where we have few lines to a. add interfaces; b. ifconfig addresses and c. quagga start

```
ip link add dummy0 type dummy
/sbin/ifconfig dummy0 1.1.1.1 netmask 255.255.255.255 up

/sbin/ifconfig eth0 100.0.14.1 netmask 255.255.255.0 up
/etc/init.d/quagga start
```

### r/etc/quagga

Where we have three files to a. activate daemons; b.configure zebra; c. configure bgpd

**daemons**

```
zebra=yes
bgpd=yes
ospfd=no
ospf6d=no
ripd=no
ripngd=no
isisd=no
babeld=no
```

**zebra.conf**

```
hostname  r
password zebra
enable password zebra
log file /var/log/zebra/zebra.log
```

**bgpd.conf**
```
hostname BGPD
password zebra
enable password zebra
log file /var/log/quagga/bgpd.log

router bgp 1
        bgp router-id 1.1.1.1
        network 1.0.0.0/8
        neighbor 100.0.14.4 remote-as 4
        neighbor 100.0.14.4 route-map rm-cust-in in
        ...

route-map rm-cust-in permit 10
route-map rm-cust-out permit 10
...
```

## Quick tricks

You can ping, for instance from AS1, all interesting addresses like this: ```for i in `seq 1 9`; do ping $i$i.$i$i.$i$i.$i -c 3 -I 11.11.11.1; done```
