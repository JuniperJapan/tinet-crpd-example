# tinet-crpd-example
## Introduction

[TINET](https://github.com/tinynetwork/tinet) is a useful tool for setup virtual network topology using docker containers.

[cRPD](https://www.juniper.net/us/en/products/routers/containerized-routing-protocol-daemon-crpd.html) is an instance of the Junos OS routing functionality instantiated inside a Linux containerized environment. cRPD provides cloud-native routing to the network. We package the cRPD software as a Docker container image.

This repository contains example topology configurations for use cRPD on TINET.

## Download cRPD

Juniper Networks provides [cRPD Free Trial](https://www.juniper.net/us/en/dm/crpd-free-trial.html) for trial use.

## Prerequisite

 * Install Docker and [TINET](https://github.com/tinynetwork/tinet)
 * Download cRPD package and obtain license keys.

## Usage

```sh
$ docker load < junos-routing-crpd-docker-amd64-23.2R1.13.tgz
Loaded image: crpd:23.2R1.13

$ git clone https://github.com/JuniperJapan/tinet-crpd-example/

# update license key
# or `export CRPD_LICENSE='xxx'` and use `sudo -E bash`
$ cd tinet-crpd-example/basic_bgp
$ vi spec.yaml

$ tinet upconf -v | sudo bash
685e76f3520c77c7eb235f2b107c853acb9048389c77a9b5d41d13d433a5a9ae
a9b35ff72a0242dfa2fdd3fa0ddb62f0a6ca82b080ee2e289dd799aa5c66d879
E20231213001: successfully added
add license complete (no errors)
Entering configuration mode
commit complete
Exiting configuration mode
E20231213001: successfully added
add license complete (no errors)
Entering configuration mode
commit complete
Exiting configuration mode

$ docker exec R1 cli show bgp summary
Threading mode: BGP I/O
Default eBGP mode: advertise - accept, receive - accept
Groups: 1 Peers: 1 Down peers: 0
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.0.0.2              65002          3          2       0       0           6 Establ
  inet.0: 0/0/0/0

$ docker exec R2 cli show bgp summary
Threading mode: BGP I/O
Default eBGP mode: advertise - accept, receive - accept
Groups: 1 Peers: 1 Down peers: 0
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.0.0.1              65001          3          2       0       0          11 Establ
  inet.0: 0/0/0/0

$ docker exec -it R1 cli
root@R1> show configuration
## Last commit: 2024-06-30 11:54:38 UTC by root
version 20230616.051920_builder.r1345402;
system {
    host-name R1;
}
interfaces {
    net0 {
        unit 0 {
            family inet {
                address 10.0.0.1/24;
            }
        }
    }
}
routing-options {
    router-id 10.255.0.1;
    autonomous-system 65001;
}
protocols {
    bgp {
        group ebgp {
            neighbor 10.0.0.2 {
                peer-as 65002;
            }
        }
    }
}

root@R1> show bgp summary
Threading mode: BGP I/O
Default eBGP mode: advertise - accept, receive - accept
Groups: 1 Peers: 1 Down peers: 0
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.0.0.2              65002         92         91       0       0       40:06 Establ
  inet.0: 0/0/0/0
```
