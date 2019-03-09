# Emerging Threats IP Blocklist Installation on Ubuntu 18.04

## System Preparation
* Configure and enable the firewall using ufw. On my system I used the commands shown below; you should select the ports and settings appropriate for your situation.
```
ufw allow 2207/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw allow in to 224.0.0.0/4
ufw allow in from 224.0.0.0/4
ufw allow from 192.168.1.245 proto udp port 137 to any
ufw enable
```
## Install ipset
```
apt update
apt install ipset
```

## emerging-ipset-update.sh
Create a home for scripts, and download this modified version of the bash script (emerging-ipset-update.txt) from [doc.emergingthreats.net](https://doc.emergingthreats.net/bin/view/Main/EmergingFirewallRules)
```
mkdir -p /root/bin
curl -s -L https://raw.githubusercontent.com/mmccarn/Ubuntu/master/root/bin/emerging-ipset-update.sh -o /root/bin/emerging-ipset-update.sh
chmod +x /root/bin/emerging-ipset-update.sh
```

## add-iptables-rules.sh
The emerging threat blocklists use ipsets for speed and efficiency.  The Ubuntu ufw firewall does not offer any mechanism for creating rules based on ipsets, so we need to create those rules manually.
[add-iptables-rules.sh](https://github.com/mmccarn/Ubuntu/blob/master/root/bin/add-iptables-rules.sh) is a script that can be run manually for testing, and should be scheduled to run at each reboot to ensure that the required firewall rules are created and the IP blocklists are loaded.

add-iptables-rules.sh:
- deletes any existing INPUT iptables rules referencing "match-set"
- makes sure that the emerging threat ipsets (blacklist and blacklistnet) exist
- inserts iptable rules into the INPUT chain to log and block any traffic from or to any IP in the blacklist or blacklistnet ipset
- runs /root/bin/emerging-ipset-update.sh to get the latest ip blocklist

```
# download 
curl -s -L https://raw.githubusercontent.com/mmccarn/Ubuntu/master/root/bin/add-iptables-rules.sh -o /root/bin/add-iptables-rules.sh
chmod +x /root/bin/add-iptables-rules.sh

# run
/root/bin/add-iptables-rules.sh
```
