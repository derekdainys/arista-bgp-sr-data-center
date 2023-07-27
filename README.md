# BGP-SR Data Center on Arista EOS

I was having trouble to find documentation on Arista's website on how to do BGP-SR. So, I decided to build a single protocol Data Center with EVPN-MPLS. BGP is used to distribute labels for MPLS and BGP is also used as overlay with EVPN.

---

My topology is attached below.

![BGP-SR Topology](/bgp-sr-topology.png)

---

Each different colored host is in a different VLAN and has to pass through the firewall to reach another VLAN. Hosts in the same VLAN can communicate to each other either via Route-Type 2 or BUM with Route-Type 3. Some hosts are multihomed using EVPN just for variety.

Firewall is also multihomed and has two separate BGP sessions with the WAN/SP router. Any traffic to the WAN must also pass the firewall.

BGP design is using iBGP with ASN 65001 across both Spines that are acting as route reflectors and Leaves.

---

To distribute BGP-SR on EOS this is the workflow in Jinja format

~~~
interface Loopback0
    ip address {{ loopback_address }}
!
route-map {{ name }} permit 10
    set segment-index {{ number }}
!
router bgp {{ asn }}
    address-family ipv4 labeled-unicast
    network {{ loopback_address }} route-map {{ name }}
!
~~~

---

Only trouble I ran in with Arista is I was unable to do IRB with EVPN-MPLS and had to mostly just use Route-Type 2 and Route-Type 5. It seems like this is possible on physical switches, but you need to access the tcam profiles for it.

Despite these limitations I was still able to add a firewall service insertion.

---