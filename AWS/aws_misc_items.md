DNS
  Used to convert human frienfly domain names into an Internet Protocol (IP) address.

  Ip addresses are used by computers to identify each other on the network.
    Ips come in 2 different forms, IPv4 and IPv6
      IPv4
        IPv4 space is a 32 bit field and has over 4 trillion different addresses.

        Too few addresses

        If an IP address is a grain of sand, IPv4 could fill a dumptruck.  For Ipv6, it could fill the mass of the sun.
      
      IPv6
        Address space of 128bits or 340 undecillion addresses

  Domain Structure
    Each domain name is made up for a series of character strings (called "labels") that are seperated by dots.

    The right-most label in a domain name is referred to as its "top-level domain" (TLD).
      Each TLD can include a second-level domain
        EX: securingthestack in www.securingthestack.com

        TLDs are controlled by the Internet Assigned Numbers Authority (IANA) in a root zone database which is essentially a db of all of the available TLDs.
          Ex: www.iana.org/domains/root/db
      And a third-level
        EX: "www" in www.securingthestack.com

      This pattern can keep going...

  Domain Registrar
    An authority that can assign domain names directly under one or more top-level domains.
      Ex: GoDaddy.com, etc.

      From the registrar, these domains are then registered with InterNIC (a service of ICANN), which enforces uniqueness of domain names across the Internet.  Each domain name becomes registered in a central database known as the WhoIS database

  DNS Zone
    Any distinct, contiguous portion of the DNS for which administrative responsibility has been delegated to a single manager.

    A DNS zone is the part of a domain for which an individual DNS server is responsible.

  What do DNS requests to name servers look like?
    But just how exactly did your computer know what webpage to display for you, and what server to pull it from?

    Your web-browser knows you typed InMotionHosting.com into the address bar.

    Your computer then uses DNS to retrieve the current nameservers for InMotionHosting.com.

    Our public nameservers; ns.inmotionhosting.com and ns2.inmotionhosting.com are retrieved.

    Your computer asks our nameservers for the A (address) record for InMotionHosting.com.

    Our public nameservers respond back with the IP address 192.145.237.216

    Your computer sends a request to that IP address along with the page you're requesting.

    Our web server hosting InMotionHosting.com then sends your web-browser the requested page.



  Common DNS Types (Dont need to know for exam)
    SOA Records
      Start of Authority record is information stored in a DNS zone about that zone.
      
      Each zone contains a single SOA record.
        The SOA record is always the first part

      Contains meta-data about the domain

    NS Records
      Name Server records

      Used by Top Level Domain servers to direct traffic to the Content DNS server which contains the authoritative DNS records.

      HERE: ADD THIS IN https://www.bytemark.co.uk/support/document_library/dnsworks/

    A Records
    CNAMES
    MX Records
    PTR Records



Tags
  Key/Value pairs that are attached to AWS resources

  Metadata (data about data)

  Tags can sometimes be inherited
    Ex: Autoscaling, CloudFormation, Elastic Beanstalk can create other resources

  If you easily want to add tags to a resource, go into the tag editor and you can edit.
    The tag editor is also a simple way to see all resources across all regions, etc.

Resource Groups
  Make it easy to group resources using the tags that are assigned to them.

  You can also group by nested tags
    Ex: People in the "developers" tag that match the sub-tab of "treamA"

  You can group resources that share one or more tags.

  Goods ideas
    Group resources by ENV, project, ect.

  Resource groups contain information such as:
    Region
    Name
    Health Checks
      If the associated resources are passing/failing
    Instance state

    If you go into "show/hide columns" you can get additional information such as (not limited to)
      For EC2
        Public/Private IP
      For ELB
        Port Configurations
      For RDS
        Database Engine, etc.

    You can dump the attributes(columns) from a resource group panel into a csv file.

  You can create a resource group right in your AWS console