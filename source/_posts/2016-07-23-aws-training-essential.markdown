---
layout: post
title: "AWS training essential"
date: 2016-07-23 09:31:56 +0800
comments: true
categories: 
---
Region < AZ(Cluster of Data Center) < Data Center

DR(災難備援)

53 is DNS Port

EC2 is a compute node, charge rate is hourly based.

Globe level:
Edge location:
  Route 53
  CloudFront
Region:
  AMI
  S3
AZ:
  EC2 EBS

EBS is the persistance storage of EC2, connect thru network, EC2 instance store is in the same rake of EC2. EBS is more recommandated. AMI will describe it's root device is EBS or instance storage.

can instance store backup.

reboot: same EC2 instance remain.
stop/start: will reassign a new EC2 instance.

Security:
  user based policy: IAM, who can do what in which resources(high recommandated, easier to manage)
  resource baseb policy: who can do what
  when two policy conflict, use the most restricted one.
  Enable MFA for privilige user

RDS is powered by EC2 and EBS behind in sceen
Auto scaling is Horizontal scaling, add or reduce number of instance 

self pace learning

run.qwiklab.com

https://742710798012.signin.aws.amazon.com/console

VPC Endpoint 可以讓VPC中的node可以不用透過public network 去連接 S3.

Security Group v.s. Network ACLs

traffic path:
instance < security group < network ACLs < route table < public IP or igw

NAT Gatwway(managed service) instead of NAT Server(actually a EC2 instance)

A record vs CNAME vs ALIAS
A record: map to IP address
ALIAS: map to DNS name

availibility -> 通常指系統
durability -> 通常指data

data at transit -> thru HTTPS to secure data when data transition
data at rest -> encrype when store data

IAM user:
  credential:
    username and password
    access key / security key
  can be a real user or a service (e.g. EC2)

IAM role:
  trusted entity: 誰可以用這個role: AWS resource, AWS account, faderation user(authentication outside AWS, but get authorization thru STS)
  policy:

Bastion Host:
  從外面連到web tier的跳板

cloudwatch logs:
  logs -> metrics filter -> metrics -> alarm -> email

Make Bastion always availiable: set ELB min:1 max:1 desired:1

managed service will more cost efficient than self managed service




