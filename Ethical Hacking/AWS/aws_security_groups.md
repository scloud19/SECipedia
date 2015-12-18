Potential AWS vulnerabilities
  Security groups are stateful
    If you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules.

    In a VPC, this also means that responses to allowed inbound traffic are allowed to flow out, regardless of outbound rules.

    If your instance (host A) initiates traffic to host B and uses a protocol other than TCP, UDP, or ICMP, your instance’s firewall only tracks the IP address and protocol number for the purpose of allowing response traffic from host B. If host B initiates traffic to your instance in a separate request within 600 seconds of the original request or response, your instance accepts it regardless of inbound security group rules, because it’s regarded as response traffic. For VPC security groups, you can control this by modifying your security group’s outbound rules to permit only certain types of outbound traffic. Alternatively, you can use a network ACL for your subnet — network ACLs are stateless and therefore do not automatically allow response traffic. For more information, see Network ACLs in the Amazon VPC User Guide.