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

## Domain Notification

This packet should distribute the current domain, the number of direct neighbors in the domain, and the check_targets with results via an UDP multicast.

Broadcasts are send on all mesh interfaces (without VXLAN's), and over 802.11s-Mesh.

Suggestion: UDPv6 Multicast in the following format:

```json
{"node_id":"","domain":"domain_code","domain_neighbors":2,"gw_tq":255,"connection_check_targets":[{"[2001:0db8::1]":true,"[2001:0db8::2]":false}]}
```

Sending and receiving is handled trough a daemon. Received messages should be stored in tmpfs with local timestamp.

Domain Notifications are not send, when "half_switch" is active.

## Procedure

The following procedure runs periodically (cron).

1.: Is the neighbor_domain_switch enabled? If no, exit here.

2.: Is "half_switch" set? If yes, go to 5

3.: Are the targets reachable? If yes, exit here.

4.: Are the targets unreachable for over neighbor_switch_offline_mins? If no, exit here.

5.: Did we already receive an domain notification not older than neighbor_switch_offline_mins? If yes, validate them and switch to the domain. If "half_switch" is set and nothing is received, exit here.

6.: Is wifi scanning enabled? If no, exit here.

7.: Do we find a known mesh SSID? Set "half_switch" and switch to the regarding primary domain. 

Notes:

- "half_switch" indicates that we already switched into the proper L2 domain, but not into the correct domain code.

- An domain notification is only valid, when at least one of the successful probes matches our own configuration.
- Domain notifications are stored by transmitting node, and always validated together.
- If we have multiple valid domain notifications with different domains, the one with the best Gateway TQ wins. If all are the same, the one with the most neighbors wins. If they also the same, the first in the list of received notifications is taken.

