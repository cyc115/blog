---
title: "Secure AWS subnets with bastion jump hosts"
date: 2017-11-11T17:02:01-06:00
draft: false
tags: ["AWS", "security"]
---
 
A bastion server is [what does it has to do with security?]

![bastion server](/img/2017-11-11/bastion-aws.png)

Today I was setting up a public subnet bastion server to access instances on my private subnet. I was able to ssh on to the bastion server but was unable to establish ssh connection from the bastion instance to the private instance. 

Here's the setup in a concise form: 

## VPC

 name        | id  | comment 
-------------|-----|---------
 network ACL | 2ec |         

## subnet

 name       | id  | vpc | route table | network ACL 
------------|-----|-----|-------------|-------------
 public SN  | d4a | 2ec | f40         | f13         
 private SN | 534 | 2ec | b2c         | 8c0         

## route table 

comment           | id  | destination | target   
------------------|-----|-------------|----------
public subnet rt  | f40 | 0.0.0.0/0   | igw-c02  
                  |     | 10.0.0.0/16 | local    
private subnet rt | b2c | 0.0.0.0/0   | nat-0a3  
                  |     | 10.0.0.0/16 | local    

## network ACLs

id of NACL-1 is f13 

 i/o      | rule | type | protocol | source    | Allow/Deny 
----------|------|------|----------|-----------|------------
 inbound  | 100  | ALL  | ALL      | 0.0.0.0/0 | Allow      
 inbound  | *    | ALL  | ALL      | 0.0.0.0/0 | Deny       
 outbound | 100  | ALL  | ALL      | 0.0.0.0/0 | Allow      
 outbound | *    | ALL  | ALL      | 0.0.0.0/0 | Deny       

id of NACL-2 is 8c0 

 i/o      | rule | type | protocol/port | source      | Allow/Deny 
----------|------|------|---------------|-------------|------------
 inbound  |  100 | SSH  | TCP/22        | 10.0.1.0/24 | Allow      
 inbound  |    * | ALL  | ALL           | 0.0.0.0/0   | Deny       
 outbound |  100 | SSH  | TCP/22        | 10.0.1.0/24 | Allow      
 outbound |    0 | ALL  | ALL           | 0.0.0.0/0   | Deny       


## IPs 
* Public Bastion (private) IP: 10.0.1.13
* Private instance IP: 10.0.0.144
    * security gorup allow port 22 (SSH) from source 0.0.0.0,::/0, and is set to private accessible


**update 1 Sun Nov 12 2017**

After some testing, it looks like the NACL (8c0) is blocking outbound ssh connection. 

When bastion establishes connection to a client on port 22, the client will choose a "TCP high" port as source port to connect to port 22. TCP high port range from >1024 to 65535. The correct solution to this problem is to open TCP outbound port 1204-65535 to 10.0.1.0/24 range. 

**update 2** 

Althrough setting up bastion server this way is correct and useful in certain scenarios (cases where you would like an extra layer of security from the Network ACL rules). it would be more flexibile to use only security group (no NACL) to manage access.





