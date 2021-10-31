title: BGP flowspec

# BGP flowspec
[RFC8955](https://datatracker.ietf.org/doc/html/rfc8955)

Disclaimer:
По BGP можно даже играть в [морской бой](https://habr.com/ru/post/415689/)...

![image-bgp-sea-​​battle](https://hsto.org/webt/t0/cb/ks/t0cbksnfb2p-wdzhfoqhcabg8vc.gif)


## Предназначение
BGP flowspec - позволяет производить некий набор действий с определенным видом трафика.
Это позволяет например противостоять DDoS атакам, контролировать полосу для специфичных ip адресов и пр. подобные действия.

### Что под "капотом"

https://blog.marquis.co/what-is-bgp-flowspec/
https://datatracker.ietf.org/doc/html/rfc8955


В RFC [RFC5575](https://datatracker.ietf.org/doc/html/rfc5575)
Определена новые пары AFI/SAFI для BGP дщцызус:
```bash
    (AFI=1, SAFI=133) for IPv4 Flow Specification
    (AFI=1,SAFI=134) for VPNv4 Flow Specification.
```

*Соответственно в NLRI могут передаваться 12 различных типов:*

Type	NLRI Component
```bash

"1    - Destination Prefix" - Defines the destination prefix to match
"2    - Source Prefix" - Defines the source prefix
"3    - IP Protocol" Contains a set of {operator, value} pairs that are used 
                  to match the IP protocol value byte in IP packets.
"4    - Port" This is defines whether TCP, UDP or both will be packets will be influenced
"5    - Destination Port" - Defines the destination port that will be influenced by FlowSpec
"6    - Source Port" - Defines the source port that will be influenced by FlowSpec
"7    - ICMP Type"
"8    - ICMP Code"
"9    - TCP flags"
"10   - Packet Length" - Match on the total IP packet length 
                       (excluding Layer 2 but including IP header)
"11	  - DSCP" - Match on the Class Of Service flag
"12	  - Fragment Encoding"
```

*В BGP extended community передается "action" - действия, которые определяют надо сделать с данными из NLRI:*

```bash
"Traffic-Rate Community"
The Traffic-Rate community is non-transitive, that tells the receiving BGP peer, 
what to rate limit matching traffic to. 
If the traffic needs to be discarded or dropped, this will be limit of 0 should be used.

"Traffic-Action Community"
The Traffic-Action community is used to sample defined traffic. 
This allows sampling and logging metrics to be collected from the FlowSpec route, 
that could be used to get a better understand of the attack traffic.

"Redirect Community"
The Redirect community allows the FlowSpec traffic to be redirected into a Virtual Routing and Forward Instance VRF. 
As the same Route-Targets and Route-Distinguisher can be used, 
you are able to import routes into a dedicated blackhole VPN or any other VPNv4.

"Traffic-Marking Community"
The Traffic-Marking community is used to modify the Differentiated Service Code Point DSCP bits 
of a transiting IP packet to the defined value. 
This could be used to set to FlowSpec routes to highest discard probability, 
allowing traffic not to dropped/discarded until co
```

*По факту на вход подаются 3 параметра:*
```bash
- "prefix"
- "type"
- "action"
```

В соответствии с данные параметрами происходит фильтрация префикса.
Например, применение его к определенному ACL или route-map в конфигурации оборудования.


