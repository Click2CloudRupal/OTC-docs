---
layout: "opentelekomcloud"
page_title: "OpenTelekomCloud: opentelekomcloud_vpc_peering_connection"
sidebar_current: "docs-opentelekomcloud-resource-vpc-peering-connection"
description: |-
  Get information on an OpenTelekomCloud VPC Peering Connection.
---

# opentelekomcloud_vpc_v1

opentelekomcloud_vpc_peering_connection provides a resource to manage a VPC Peering Connection resource..

> **Note:** For cross-account (requester's account differs from the accepter's account) or inter-region VPC Peering Connections use the opentelekomcloud_vpc_peering_connection resource to manage the requester's side of the connection and use the opentelekomcloud_vpc_peering_connection_accepter resource to manage the accepter's side of the connection.

## Example Usage

```hcl
resource "aws_vpc_peering_connection" "foo" {
  peer_owner_id = "${var.peer_owner_id}"
  peer_vpc_id   = "${aws_vpc.bar.id}"
  vpc_id        = "${aws_vpc.foo.id}"
}
```

Basic usage with connection options:
```hcl

resource "aws_vpc_peering_connection" "foo" {
  peer_owner_id = "${var.peer_owner_id}"
  peer_vpc_id   = "${aws_vpc.bar.id}"
  vpc_id        = "${aws_vpc.foo.id}"

  accepter {
    allow_remote_vpc_dns_resolution = true
  }

  requester {
    allow_remote_vpc_dns_resolution = true
  }
}
```

Basic usage with tags:
```hcl
resource "aws_vpc_peering_connection" "foo" {
  peer_owner_id = "${var.peer_owner_id}"
  peer_vpc_id   = "${aws_vpc.bar.id}"
  vpc_id        = "${aws_vpc.foo.id}"
  auto_accept   = true

  tags {
    Name = "VPC Peering between foo and bar"
  }
}

resource "aws_vpc" "foo" {
  cidr_block = "10.1.0.0/16"
}

resource "aws_vpc" "bar" {
  cidr_block = "10.2.0.0/16"
}
```

Basic usage with region:
```hcl
resource "aws_vpc_peering_connection" "foo" {
  peer_owner_id = "${var.peer_owner_id}"
  peer_vpc_id   = "${aws_vpc.bar.id}"
  vpc_id        = "${aws_vpc.foo.id}"
  peer_region   = "us-east-1"
}

resource "aws_vpc" "foo" {
  provider   = "aws.us-west-2"
  cidr_block = "10.1.0.0/16"
}

resource "aws_vpc" "bar" {
  provider   = "aws.us-east-1"
  cidr_block = "10.2.0.0/16"
}
```
## Argument Reference

> **Note:** Modifying the VPC Peering Connection options requires peering to be active. An automatic activation can be done using the auto_accept attribute. Alternatively, the VPC Peering Connection has to be made active manually using other means. See notes below for more information.

The following arguments are supported:

- peer_owner_id - (Optional) The AWS account ID of the owner of the peer VPC. Defaults to the account ID the AWS provider is currently connected to.
- peer_vpc_id - (Required) The ID of the VPC with which you are creating the VPC Peering Connection.
- vpc_id - (Required) The ID of the requester VPC.
- auto_accept - (Optional) Accept the peering (both VPCs need to be in the same AWS account).
- peer_region - (Optional) The region of the accepter VPC of the [VPC Peering Connection]. auto_accept must be false, and use the aws_vpc_peering_connection_accepter to manage the accepter side.
- accepter (Optional) - An optional configuration block that allows for VPC Peering Connection options to be set for the VPC that accepts the peering connection (a maximum of one).
- requester (Optional) - A optional configuration block that allows for VPC Peering Connection options to be set for the VPC that requests the peering connection (a maximum of one).
- tags - (Optional) A mapping of tags to assign to the resource.

Accepter and Requester Arguments

> **Note:** When enabled, the DNS resolution feature requires that VPCs participating in the peering must have support for the DNS hostnames enabled. This can be done using the enable_dns_hostnames attribute in the aws_vpc resource. See Using DNS with Your VPC user guide for more information.

- allow_remote_vpc_dns_resolution - (Optional) Allow a local VPC to resolve public DNS hostnames to private IP addresses when queried from instances in the peer VPC.
- allow_classic_link_to_remote_vpc - (Optional) Allow a local linked EC2-Classic instance to communicate with instances in a peer VPC. This enables an outbound communication from the local ClassicLink connection to the remote VPC.
- allow_vpc_to_remote_classic_link - (Optional) Allow a local VPC to communicate with a linked EC2-Classic instance in a peer VPC. This enables an outbound communication from the local VPC to the remote ClassicLink connection.
 
## **Attributes Reference**

The following attributes are exported:

- id - The ID of the VPC Peering Connection.
- accept_status - The status of the VPC Peering Connection request.

## **Notes**

If both VPCs are not in the same AWS account do not enable the auto_accept attribute. The accepter can manage its side of the connection using the aws_vpc_peering_connection_accepter resource or accept the connection manually using the AWS Management Console, AWS CLI, through SDKs, etc.

## **Import**

VPC Peering resources can be imported using the vpc peering id, e.g.

> $ terraform import aws_vpc_peering_connection.test_connection pcx-111aaa111