Add Public IP to container
====

This document shows how to add public ip to container. This code is very simple solution with IPTables. (a.k.a Floating IP)




```
#!/bin/bash
if [ "$#" -ne "2" ]; then
  echo "$0 [CID] [IPZ]"
  exit 0
fi

CID=$1
IPZ=$2

INADDR=`lxc info ${CID} | grep eth0 | awk '{print $3}'`
if [ "${#INADDR}" -eq "0" ]; then
  echo "no such CONTAINER : ${CID}"
  exit 0
fi

CNT=`ip addr  | grep "${IPZ}/32" | wc -l`
if [ "$CNT" -eq "0" ]; then
  #echo "no such IP ADDR : ${IPZ}"
  ip a add ${IPZ}/32 dev eth0
  #exit 0
fi

iptables-save | grep -v "IPFW_${IPZ}" | iptables-restore


LXCSUBNET=`ip route | grep lxcbr0 | awk '{print $1}'`
iptables -t nat -A PREROUTING -d ${IPZ}/32 -p tcp -m comment --comment "IPFW_${IPZ}" -j DNAT --to-destination ${INADDR}
iptables -t nat -A POSTROUTING -s "${LXCSUBNET}" -d ${INADDR} -p tcp -m comment --comment "IPFW_${IPZ}" -j SNAT --to-source ${IPZ}
```
