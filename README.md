# DS-Lite Auto Provisioning

This program is a sample implementation of [IPv6マイグレーション技術の国内標準プロビジョニング方式 【第1.1版】](https://github.com/v6pc/v6mig-prov/blob/1.1/spec.md) for Junos router.

## Usage

 - Copy `dslite_autoconfig.py` to `/var/db/scripts/event/`
 - Change script permission to 755 (`chmod 755 /var/db/scripts/event/dslite_autoconfig.py`).
 - Load following configuration to Junos box(need to change NTT_EAST, ge-0/0/0.0, and USERNAME to appropriate value).

```
set event-options generate-event dslite_update time-interval 1800
set event-options policy update-dslite-config events dslite_update
set event-options policy update-dslite-config then event-script dslite_autoconfig.py arguments -area NTT_EAST
set event-options policy update-dslite-config then event-script dslite_autoconfig.py arguments -external-interface ge-0/0/0.0
set event-options event-script file dslite_autoconfig.py python-script-user USERNAME
```

 - Other required configuration(need to change interface name and addresses to appropriate value):
```
set interfaces ge-0/0/0.0 unit 0 family inet6 address 2001:db8::2/64
set routing-options rib inet6.0 static route ::/0 next-hop 2001:db8::1
set routing-options static route 0/0 next-hop ip-0/0/0.0
```

## Verified VNEs
 - Internet Multifeed Transix (NTT East, Flet's Next)
   - AFTR address is an IPv6 address.

## Caveats
 - Covers only vendorid, product, version and capability parameters. Persistent token and authentication is not implemented.
 - TLS certificate validation is disabled.
 - Currently, SRX doesn't support IPIP6 tunnel. This script works only on MX series router.

## Acknowledgement
It contains pieces of code from the following softwares:

 -  [Original DNS Client Implementation(vmartyanov/dns)](https://github.com/vmartyanov/dns) by Vladimir Martyanov
