DE = Dev Exam

ELB (Elastic Load Balancer)
  Ports that ELB can use (DE): 25,80, 443, 1024-65535

  After sitting up the ELB, make sure to change your 'A' records in Route 53 so they point to the DNS name of the ELB.

  You can place EC2 instances behind the ELB

  Multiple SSL Certs can be placed on an ELB
    They don't have to be terminated on the EC2s that are behind the ELB.
      If you're terminating multiple SSL certs on one apache server, you need to set up an IP for every SSL cert and then do mapping.  ELBs mitigate this.

  Pricing (Know for DevExam)
    You are charged by the hour and on a per Gb basis of usage.

    Services which are free include CloudFormation, Elastic Beanstalk and Autoscaling
      However, the resources that these services spin-up can be charged to you.

