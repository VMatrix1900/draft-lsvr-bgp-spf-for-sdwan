---
stand_alone: true
category: bcp
submissionType: IETF
ipr: trust200902
lang: en

title: Usage of BGP-LS-SPF in Multi-segment SD-WAN
abbrev: BGP-LS-SPF for Multi-segment SD-WAN
docname: draft-sheng-lsvr-bgp-spf-for-sdwan-00
obsoletes:
updates:
# date: 2022-02-02 -- date is filled in automatically by xml2rfc if not given

area: routing
workgroup: LSVR

kw:
  - SDWAN

author:
 -
  ins: C. Sheng
  name: Cheng Sheng
  organization: Huawei
  street: Beiqing Road
  city: Beijing
  email: shengcheng@huawei.com
 -
  ins: H. Shi
  name: Hang Shi
  organization: Huawei
  email: shihang9@huawei.com
  role: editor
  street: Beiqing Road
  city: Beijing
  country: China

--- abstract

This document introduces the usage of BGP-LS-SPF protocol in multi-segment SD-WAN scenarios. It allows SD-WAN tunnels to be published as logical links, which can cross the internet, MPLS networks, and various operator network. The BGP-LS-SPF protocol can construct an overlay network topology for logical links and physical links across these heterogeneous networks, and calculate the reachability routes of overlay network nodes based on this topology.

--- middle

# Introduction {#intro}

As pointed out in {{?I-D.draft-ietf-rtgwg-net2cloud-problem-statement}}, enterprises are migrating their workloads to cloud service. The enterprise branch interconnection and enterprise site to cloud DC connection may cross heterogeneous network such as operator networks, enterprise-owned backbone networks or direct connection lines.

For large enterprises to access the cloud service and interconnect their branches, a PoP GWs network can be built to provide multi-cloud, multi-tenant, and multi-branch interconnection. Depending on the geographical distribution of the enterprise branches, the PoP GWs network may be a cross-regional or even a global network. The PoP GW can be connected to the operator network or the enterprise-owned backbone network. The PoP GWs devices can also be directly connected through dedicated lines.

According to {{!I-D.draft-ietf-bess-bgp-sdwan-usage}}, SD-WAN tunnels can be established between two GWs devices connected to the operator network, MPLS VPN network, or internet network through the WAN ports of the two PoP GWs devices. All GWs are under the control of one BGP instance. {{!I-D.draft-ietf-idr-sdwan-edge-discovery}} defines the mechanism for SD-WAN edges to discover each other's properties via BGP update through RR. This allows the interconnection between enterprise branches and multi-cloud to pass through multiple SD-WAN tunnels or direct connection lines, as shown in {{pop-gw}}.

This draft provides a way to use the BGP-LS-SPF protocol to collect the identification of PoP GW device node and the topology of SD-WAN tunnel and direct connection lines. In this way, each PoP GW device can learn the PoP GWs network topology, and calculate the route to any other PoP GW.

# Terminology

This specification reuses terms defined in {{Section 5.2 of ?I-D.draft-ietf-lsvr-bgp-spf}} including BGP-LS-SPF Node NLRI, BGP-LS-SPF Link NLRI, Dijkstra Algorithm.

- PoP GW: Point of Presence Gateway
- SD-WAN: Software Defined Wide Area Network. In this document, "SD-WAN" refers to policy-driven transporting IP packets over multiple different underlay networks to get better WAN bandwidth management, visibility and control.
- RR: Route Reflector
- Cloud DC: Off-Premise Data Centers that usually host applications and workload owned by different organizations or tenants.

## Requirements Language

{::boilerplate bcp14-tagged}

# Usage of BGP-LS-SPF in Multi-segment SD-WAN

~~~
      + - - - +- - - - - - - - - - - -|RR| - - - - - - - - - -+ - - - - +
      |       |                        |                      |         |
      |    +--|--+                  +--|--+                +--|--+      |
      |    | GW1 |------------------| GW2 | -Physical link-| GW3 |      |
      |    +--|--+10.1.1.1  20.1.1.1+-----+                +--|--+      |
      |       |     SD-WAN Tunnel  /                 Physical |30.1.1.1 |
      |       |    ----------------                      Link |         |
      |       |   / over Internet                             |40.1.1.1 |
      |    +--|--+                                         +--|--+      |
      |+--+| GW5 |---------SD-WAN Tunnel over MPLS---------| GW4 |+-----+
           +--|--+                                         +--|--+
              |                                               |
+ - -+      + - -+                                         + - -+     +- - - +
|User|------|CPE1|                                         |CPE2|-----| APPs |
+ - -+      + - -+                                         + - -+     + - - -+
~~~
{: #pop-gw  title="PoP GWs network"}

As shown in {{pop-gw}}, GW1, GW2, GW5 are connected to the same internet/ISP network. The GW2 and GW3 are connected through direct dedicated links. GW5 and GW4 are connected by MPLS VPN. BGP-SD-WAN neighbors are established between GWs through RR. BGP-LS-SPF neighbors are established between each GW and RR. SD-WAN tunnel links are established between GWs through BGP-SD-WAN neighbors reflecting SD-WAN routes(see {{!I-D.draft-ietf-idr-sdwan-edge-discovery}}), as shown in the SD-WAN Tunnel between GW1 and GW2 with WAN port IP addresses of 10.1.1.1 and 20.1.1.1, respectively. GW nodes reflect the SD-WAN tunnel topology information to all GWs, including dedicated line-connected GWs, through BGP-LS-SPF neighbors with RR.

GW2-GW3-GW4 are connected through dedicated lines. BGP-LS-SPF neighbors are established between GWs through dedicated lines, and also between GWs and RR. The BGP-LS-SPF neighbors between dedicated lines are used to discover the topology information of the dedicated lines, such as the direct link with port IP addresses of 30.1.1.1 and 40.1.1.1 between GW3 and GW4 shown in the figure. The dedicated line topology information is reflected to all GWs, including SD-WAN tunnel-connected GWs, through BGP-LS-SPF neighbors with RR.

The BGP-LS-SPF LINK NLRI is used to carry the two endpoint IP address of the SD-WAN tunnel or dedicated lines. The BGP-LS-SPF NODE NLRI is used to carry PoP GW device node identification. They are advertised to other GWs through the RR. In this way, all GW learns the topology of whole PoP GWs network and can calculate the next hop to any other GW using Dijkstra Algorithm.

# Security Considerations

This document does not introduce any new security considerations.

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgements

The authors would like to thank Donglei Pang for his contribution to the document.
