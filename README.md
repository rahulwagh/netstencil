# NetStencil by jhooq

**Network design canvas for cloud and data centre together.**
VPCs and VNets, trust zones, firewalls, switches, leased lines and the circuits that join them, drawn
as a model the tool can validate and export, not just a picture.

- Tool (from phase 1): https://jhooq.com/tools/netstencil/
- Documentation: https://jhooq.com/tools/netstencil/docs/
- Posts and videos that use it: https://jhooq.com/ and https://www.youtube.com/@jhooq

This repository holds the **open design file format**: the JSON schema, sample designs and, as they
land, the command-line importers and exporters (Terraform, NetBox, draw.io, CSV). A design you make
in NetStencil is always yours to read, move and generate from without the tool.

The application itself (the canvas, the wizards, the validation engine, the catalogue and the team
backend) is not in this repository.

## The format in one look

```json
{
  "schema": "netstencil/1",
  "sites":    [{ "id": "s_dc1", "kind": "data-centre", "name": "DC1 Frankfurt" }],
  "zones":    [{ "id": "z_dmz", "kind": "trust-zone", "trust": "dmz", "site": "s_dc1", "cidrs": ["10.10.0.0/22"] }],
  "networks": [{ "id": "n_web", "kind": "vlan", "zone": "z_dmz", "cidr": "10.10.0.0/24", "vlanId": 110 }],
  "nodes":    [{ "id": "nd_fw1", "category": "firewall", "vendor": "Check Point", "zone": "z_dmz", "interfaces": [] }],
  "links":    [{ "id": "l_dx", "layer": "l3", "kind": "direct-connect", "a": { "node": "nd_ce" }, "b": { "node": "nd_vgw" }, "bandwidthMbps": 1000 }],
  "flows":    [{ "id": "f_web", "from": "z_red", "to": "n_web", "via": ["nd_fw1"], "decision": "inspect" }]
}
```

- [schema/design.schema.json](schema/design.schema.json): JSON Schema 2020-12 for `netstencil/1`.
- [samples/hybrid-minimal.netstencil.json](samples/hybrid-minimal.netstencil.json): one VPC with two
  Availability Zones, one data centre with red, DMZ, green and management zones behind a Check Point
  pair and Arista cores, joined by a 1 Gbps Direct Connect with BGP, three flows and an inventory.

Validate a file:

```bash
npx --yes -p ajv-cli@5 -p ajv-formats@2 ajv validate --spec=draft2020 -c ajv-formats -s schema/design.schema.json -d samples/hybrid-minimal.netstencil.json
```

## Related tools from jhooq

- [VPC Subnet Planner](https://jhooq.com/tools/vpc-subnet-planner/) ([repo](https://github.com/rahulwagh/vpc-subnet-planner)) - the subnet packing and reserved-address rules NetStencil builds on.
- [Kubernetes IP Calculator](https://jhooq.com/tools/kubernetes-ip-calculator/) ([repo](https://github.com/rahulwagh/kubernetes-ip-calculator)) - sizes the cluster networks NetStencil places.

## Icons and trademarks

Cloud icons in the tool come from the official AWS, Azure and Google Cloud architecture icon sets
under their published terms, unmodified. Vendor logos are not shipped: every appliance category has
a neutral shape and the vendor and model are the label. Teams can attach their own icon pack.
NetStencil and the J→Hooq logo are trademarks of Rahul Wagh.

## Licence

[Functional Source License, Version 1.1, MIT Future License](LICENSE.md). You may read, use, modify
and redistribute this repository for any purpose except a competing product or service; two years
after each release that release becomes available under the MIT licence.
