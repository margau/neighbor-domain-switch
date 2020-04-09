# neighbor-domain-switch
Enables autonomous domain switiching based on neighbour domains.

## Scope

This (future package) should enable gluon nodes in multidomain networks to "roam" between different domains of the same community.

## Use Case

- An Node is mobile, and moves around between different domains with different mesh-partners.
- Only the VPN-Node of an larger mesh is manually reconfigured, the remaining part of the mesh should follow without manual intervention.
- A node is offline for a longer time, and therefore misses an domain migration of his "old" mesh. The node should reconnect in the new mesh.

## Status

At the moment, only the proposed procedure exists (this document), and should provide a base for an discussion.

## Configuration

neighbor_domain_switch: Boolean, if the switch is enabled

neighbor_switch_offline_mins: Integer, Switch after this time in offline state. Default: 5 Minutes

connection_check_targets: IPv6-Targets to check to determine online/offline state

neighbor_switch_wifi_enabled: Also trying to figure out neighbors by scanning for mesh SSID's

## Domain Broadcast

This packet should distribute the current domain, the number of direct neighbors in the domain, and the check_targets with results via an UDP multicast.

Broadcasts are send on all mesh interfaces (without VXLAN's), and over 802.11s-Mesh.

Suggestion: UDPv6 Multicast in the following format:

```json
{"domain":"domain_code","domain_neighbors":2,"connection_check_targets":[{"[2001:0db8::1]":true,"[2001:0db8::2]":false}]}
```

## Procedure

*ToDo*

