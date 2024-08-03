---
title: adventures of wireguard peer for a single port service
author: vbatts
layout: post
date: 2022-12-17T11:56:00+00:00
tags:
  - Linux
tags:
  - docker
  - wireguard
  - iptables
comments:
  host: fosstodon.org
  username: vbatts
  id: 109530085740732489
---

## Overview

> Want to peer wireguard networks, and isolate it down to a single port of a service that is visible on that interface.

For this exercise, we want to expose only port 8096 TCP.

***TL;DR;*** it was not just as simple as the first examples you see here. Read to the end for the solution.

Also!: I'm no expert on all this, so please reach out to me with corrections or improvements vbatts at hashbangbash dot com

## keep it simple

If you have an interface on a host that you may only want inbound access to a single port, it could hopefully look as easy as:

```shell
iptables -A INPUT -i "${interface}" -p tcp --dport 8096 -j ACCEPT
iptables -A INPUT -i "${interface}" -p tcp -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -i "${interface}" -p all -j REJECT
```

This looks nice & simple, and wishful thinking.
But with these kinds of rules, we all *wish* for simple, and often find that it's a quick slope.

Implied in these kinds of iptables rules is that `filter` is the default table.
Looking at the [frozentux tutorial, including traversing tables](https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html#TRAVERSINGOFTABLES), there is a flow and priority of the tables, and their chains.
With this simplest case, most of the time appending (`-A`) or inserting (`-I`) to `filter INPUT` may well be all that is needed.

Additionally, the [Arch Linux docs on iptables](https://wiki.archlinux.org/title/Iptables#Basic_concepts) are also super useful.

## try it out

The service that is listening on tcp port 8096 is already running on the host in a docker container.
So, straight away we could guess there is some magic at play but hopefully not a lot as this seems like a potentially common enough [`#selfhosting`](https://fosstodon.org/tags/selfhosting) adventure.

### wireguard peers

The two (or more) hosts in this situation are communicating over [wireguard](https://www.wireguard.com/).
If you're not familiar, take a look, install it, play with it.
It's in the upstream Linux kernel, and packaged for most every distro.

The basics are that rather than classic server/client, you instead have peers that expect certain CIDR ranges to be used/accessible on a peer by peer basis.

Any services you have exposed on say `0.0.0.0` will be visible on this interface.
It is useful to use the `PostUp=`/`PostDown=` settings in the wireguard `[Interface]` to add firewalling or dns resolution commands.

So, for the host serving tcp port 8096 will have `PostUp` to expose this single port and drop the rest; and corresponding clean-up/tear-down in the `PostDown`.

A naive first pass at this based on what [some blogs](https://www.stavros.io/posts/how-to-configure-wireguard/) or posts suggest end up like:

```ini
PostUp = iptables -A INPUT -i %i -p tcp --dport 8096 -j ACCEPT; iptables -A INPUT -i %i -p tcp -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT; iptables -A INPUT -i %i -p all -j REJECT
PostDown = iptables -D INPUT -i %i -p tcp --dport 8096 -j ACCEPT; iptables -D INPUT -i %i -p tcp -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT; iptables -D INPUT -i %i -p all -j REJECT
```

Which put's this all in a single place, but is gross as a one liner.
If any one of the first two calls fails it won't bail.

With the wireguard peers configured (pasting their public keys, etc.) then bring up the interface:

```shell
> wg-quick up wg1
[#] ip link add wg1 type wireguard
[#] wg setconf wg1 /dev/fd/63
[#] ip -4 address add 10.1.1.5/24 dev wg1
[#] ip link set mtu 1420 up dev wg1
[#] iptables -A INPUT -i wg1 -p tcp --dport 8096 -j ACCEPT; iptables -A INPUT -i wg1 -p tcp -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT; iptables -A INPUT -i wg1 -p all -j REJECT
> echo $?
0
```

Then you can check for peer status in the `wg` command.

### did it blend

That command was successful.
The output of `iptables -L -v -n` shows the rules.

From the opposing peer, sure enough I can reach the 10.1.1.5 host over this peered wireguard interface and can access the `:8096` service, **but** also every other service is as well.

For test:

```shell
> telnet 10.1.1.5 873
Trying 10.1.1.5...
Connected to 10.1.1.5.
Escape character is '^]'.
@RSYNCD: 31.0
^]
```

_Nope!_

`<insert many hours of search and futzing with the "filter INPUT/OUTPUT" rules>` (like quite a few hours).
But it largely blind attempts.
I need to see what the journey of these packets actually is.

## tracing

[Script to Trace Iptables Packet Flow](https://msazure.club/script-to-trace-iptables-packet-flow/) got me pointed in a direction, but on debian `/var/log/kern.log` does not have the trace events. And despite `iptables` now being a frontend for `nf_tables`, the `nftables` package is not actually installed on this bullseye server ...

*(With minor edit)*:

```shell
#!/bin/sh
if [ $# != "2" ] || ! [ "${2}" -eq "${2}" ] 2> /dev/null
then
    if [ ${1} != "enable" ] && [ ${1} != "disable" ]
    then
        echo "Usage: ${0} [enable|disable] port"
        exit 1
    fi
fi

if [ ${1} = "enable" ]
then
    modprobe nf_log_ipv4
    sysctl -w net.netfilter.nf_log.2=nf_log_ipv4
    iptables -t raw -A PREROUTING -p tcp --dport ${2} -j TRACE
    iptables -t raw -A OUTPUT -p tcp --dport ${2} -j TRACE
    iptables -t raw -A PREROUTING -p udp --dport ${2} -j TRACE
    iptables -t raw -A OUTPUT -p udp --dport ${2} -j TRACE
    echo "iptables trace is enabled for port ${2}"
    echo "run \"nft monitor trace\" to view"
else
    iptables -t raw -D OUTPUT -p tcp --dport ${2} -j TRACE
    iptables -t raw -D PREROUTING -p tcp --dport ${2} -j TRACE
    iptables -t raw -D OUTPUT -p udp --dport ${2} -j TRACE
    iptables -t raw -D PREROUTING -p udp --dport ${2} -j TRACE
    sysctl -w net.netfilter.nf_log.2=NONE
    echo "iptables trace is disabled for port ${2}"
fi
```

Which is *something*, but if you do more than one port TRACE then the `sysctl`'s will stomp on each other. Whatever. For now let's trace.

```
root@infra1:~# ./iptables-trace.sh enable 873
net.netfilter.nf_log.2 = nf_log_ipv4
iptables trace is enabled for port 873
run "nft monitor trace" to view
```

So, once I add a TRACE to something like port 873 (rsync) to test if it still succeeds, then I open a trace monitor and capture the output:

```shell
nft monitor trace |& tee nft-trace.log
```

(More info on [tracing](https://www.opsist.com/blog/2015/08/11/how-do-i-see-what-iptables-is-doing.html))

```
trace id 37860211 ip raw PREROUTING packet: iif "wg1" ip saddr 10.1.1.4 ip daddr 10.1.1.5 ip dscp cs0 ip ecn not-ect ip ttl 64 ip id 61666 ip length 60 tcp sport 41334 tcp dport 873 tcp flags == syn tcp window 64860
trace id 37860211 ip raw PREROUTING rule meta l4proto tcp tcp dport 873 counter packets 7 bytes 387 meta nftrace set 1 (verdict continue)
trace id 37860211 ip raw PREROUTING verdict continue
trace id 37860211 ip raw PREROUTING policy accept
trace id 37860211 ip nat PREROUTING packet: iif "wg1" ip saddr 10.1.1.4 ip daddr 10.1.1.5 ip dscp cs0 ip ecn not-ect ip ttl 64 ip id 61666 ip length 60 tcp sport 41334 tcp dport 873 tcp flags == syn tcp window 64860
trace id 37860211 ip nat PREROUTING rule fib daddr type local counter packets 13941703 bytes 1645794800 jump DOCKER (verdict jump DOCKER)
trace id 37860211 ip nat DOCKER rule iifname != "br-c4974cabc2ec" meta l4proto tcp tcp dport 873 counter packets 3 bytes 180 dnat to 172.29.0.5:873 (verdict accept)
trace id 37860211 ip filter FORWARD packet: iif "wg1" oif "br-c4974cabc2ec" ip saddr 10.1.1.4 ip daddr 172.29.0.5 ip dscp cs0 ip ecn not-ect ip ttl 63 ip id 61666 ip length 60 tcp sport 41334 tcp dport 873 tcp flags == syn tcp window 64860
trace id 37860211 ip filter FORWARD rule counter packets 3371530 bytes 2261746576 jump DOCKER-USER (verdict jump DOCKER-USER)
trace id 37860211 ip filter DOCKER-USER verdict return
trace id 37860211 ip filter FORWARD rule counter packets 3371530 bytes 2261746576 jump DOCKER-ISOLATION-STAGE-1 (verdict jump DOCKER-ISOLATION-STAGE-1)
trace id 37860211 ip filter DOCKER-ISOLATION-STAGE-1 verdict return
trace id 37860211 ip filter FORWARD rule oifname "br-c4974cabc2ec" counter packets 10 bytes 584 jump DOCKER (verdict jump DOCKER)
trace id 37860211 ip filter DOCKER rule iifname != "br-c4974cabc2ec" oifname "br-c4974cabc2ec" meta l4proto tcp ip daddr 172.29.0.5 tcp dport 873 counter packets 3 bytes 180 accept (verdict accept)
trace id 37860211 ip nat POSTROUTING packet: iif "wg1" oif "br-c4974cabc2ec" ip saddr 10.1.1.4 ip daddr 172.29.0.5 ip dscp cs0 ip ecn not-ect ip ttl 63 ip id 61666 ip length 60 tcp sport 41334 tcp dport 873 tcp flags == syn tcp window 64860
trace id 37860211 ip nat POSTROUTING verdict continue
trace id 37860211 ip nat POSTROUTING policy accept
```

Clearly it was making it in first on this docker nat rule 😖

Let's look at the flow of rules.
Again let's look at the [frozentux tutorial, including traversing tables](https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html#TRAVERSINGOFTABLES).

<img src=":/a8c8f3cb1e674639947c158c157e3aa3" alt="244b9b3193896921eb1164f5c71ab9c5.png" width="290" height="493" class="jop-noMdConv">

You can see from the TRACE and this flow diagram, that it *completely* bypasses the filter INPUT path, which is where most default rules land.

Looking through `iptables-save` of the `*nat` (trimming as this machine has a number of rules):

```iptables
...
*nat
:PREROUTING ACCEPT [0:0]
:INPUT ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]
:DOCKER - [0:0]
-A PREROUTING -m addrtype --dst-type LOCAL -j DOCKER
-A OUTPUT ! -d 127.0.0.0/8 -m addrtype --dst-type LOCAL -j DOCKER
-A POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE
-A POSTROUTING -s 172.31.0.0/16 ! -o br-0ca3b3457de9 -j MASQUERADE
...
```

## the home stretch

The task now is to get a rule in earlier on this nat PREROUTING to handle anything on this wireguard interface specifically.
Ideally, a single place to make an allowlist of services.

[post on how they work around docker nat prerouting](https://unix.stackexchange.com/a/435174)
excerpt from `/etc/wireguard/wg1.conf`:

```ini
[Interface]
Address = 10.1.1.5/32
ListenPort = 1777
PrivateKey = <REDACTED>
PostUp   = /etc/wireguard/%i.sh up
PostDown = /etc/wireguard/%i.sh down
...
```

and the whole of `/etc/wireguard/wg1.sh`:

```shell
#!/bin/bash
set -eux

action="${1:-}"
interface="$(basename $0 .sh | cut -d - -f 1)"

if [ "${action}" = "up" ] ; then
  iptables -t nat -N "DOCKER-${interface}"
  iptables -t nat -I PREROUTING -i "${interface}" -j RETURN
  iptables -t nat -I PREROUTING -i "${interface}" -j "DOCKER-${interface}"

  # this is the only service that is run on Docker that we want to expose
  iptables -t nat -I "DOCKER-${interface}" -p tcp -m tcp --dport 8096 -j DOCKER
  # and everything else _should_ be dropped in the prerouting chain above

  iptables -t filter -A INPUT -i "${interface}" -p tcp --dport 8096 -j ACCEPT
  iptables -t filter -A INPUT -i "${interface}" -p all -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
  iptables -t filter -A INPUT -i "${interface}" -p icmp -j ACCEPT
  iptables -t filter -A INPUT -i "${interface}" -p all -j REJECT
  iptables -t filter -I FORWARD 1 -i "${interface}" -s 10.1.1.0/24 -d 10.1.1.0/24 -j ACCEPT
  iptables -t filter -I FORWARD 2 -o "${interface}" -s 10.1.1.0/24 -d 10.1.1.0/24 -j ACCEPT
  iptables -t filter -A FORWARD -i "${interface}" -m state --state ESTABLISHED,RELATED -j ACCEPT
  iptables -t filter -A FORWARD -i "${interface}" -j DROP
  iptables -t filter -A FORWARD -o "${interface}" -j DROP
elif [ "${action}" = "down" ] ; then
  iptables -t filter -D FORWARD -o "${interface}" -j DROP ||:
  iptables -t filter -D FORWARD -i "${interface}" -j DROP ||:
  iptables -t filter -D FORWARD -i "${interface}" -m state --state ESTABLISHED,RELATED -j ACCEPT ||:
  iptables -t filter -D FORWARD -o "${interface}" -s 10.1.1.0/24 -d 10.1.1.0/24 -j ACCEPT ||:
  iptables -t filter -D FORWARD -i "${interface}" -s 10.1.1.0/24 -d 10.1.1.0/24 -j ACCEPT ||:
  iptables -t filter -D INPUT -i "${interface}" -p tcp --dport 8096 -j ACCEPT ||:
  iptables -t filter -D INPUT -i "${interface}" -p icmp -j ACCEPT ||:
  iptables -t filter -D INPUT -i "${interface}" -p all -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT ||:
  iptables -t filter -D INPUT -i "${interface}" -p all -j REJECT ||:

  iptables -t nat -D PREROUTING -i "${interface}" -j RETURN ||:
  iptables -t nat -D PREROUTING -i "${interface}" -j "DOCKER-${interface}" ||:
  iptables -t nat -F "DOCKER-${interface}" ||:
  iptables -t nat -X "DOCKER-${interface}" ||:
else
  echo "[error] specify 'up' or 'down'"
  exit 1
fi

```

BOOM!

```
trace id 4cc0afaa ip raw PREROUTING packet: iif "wg1" ip saddr 10.1.1.4 ip daddr 10.1.1.5 ip dscp cs0 ip ecn not-ect ip ttl 64 ip id 10304 ip length 60 tcp sport 47410 tcp dport 873 tcp flags == syn tcp window 64860 
trace id 4cc0afaa ip raw PREROUTING rule meta l4proto tcp tcp dport 873 counter packets 0 bytes 0 meta nftrace set 1 (verdict continue)
trace id 4cc0afaa ip raw PREROUTING verdict continue 
trace id 4cc0afaa ip raw PREROUTING policy accept 
trace id 4cc0afaa ip nat PREROUTING packet: iif "wg1" ip saddr 10.1.1.4 ip daddr 10.1.1.5 ip dscp cs0 ip ecn not-ect ip ttl 64 ip id 10304 ip length 60 tcp sport 47410 tcp dport 873 tcp flags == syn tcp window 64860 
trace id 4cc0afaa ip nat PREROUTING rule iifname "wg1" counter packets 0 bytes 0 jump DOCKER-wg1 (verdict jump DOCKER-wg1)
trace id 4cc0afaa ip nat DOCKER-wg1 verdict continue 
trace id 4cc0afaa ip nat PREROUTING verdict return 
trace id 4cc0afaa ip nat PREROUTING policy accept 
trace id 4cc0afaa ip filter INPUT packet: iif "wg1" ip saddr 10.1.1.4 ip daddr 10.1.1.5 ip dscp cs0 ip ecn not-ect ip ttl 64 ip id 10304 ip length 60 tcp sport 47410 tcp dport 873 tcp flags == syn tcp window 64860 
trace id 4cc0afaa ip filter INPUT rule iifname "wg1" counter packets 0 bytes 0 jump wg1-state (verdict jump wg1-state)
trace id 4cc0afaa ip filter wg1-state verdict return 
trace id 4cc0afaa ip filter INPUT rule iifname "wg1" counter packets 0 bytes 0 reject (verdict drop)
```

## conclusion

Well, I learned about tracing by actually putting my hands on it, rather than just aware of its existence. Also, now this interface is trimmed down to the single port. But on the whole was way more investigation than I bargained for. As the progremmers' credo goes: **"we do these things not because they are easy, but because we thought they were going to be easy".**

