Consolidated Billing
  Where there are multiple AWS accounts ("linked accounts") that are linked to a "paying account"

  The paying account is completely independent, and can't access the resources of the other accounts (by default)
    The linked accounts are also independent from eachother

  Limits
    20 linked accounts
      Can raise a ticket with AWS to increase (as with most resources)

  Advantages
    Very easy to track charges of various departments and allocate costs.
      Ex: My dev ENV is costing me X

    Volume pricing discount across all accounts
      Ex: First 1 TB/month on S3 is $.03/GB.
        1TB-49TB/month is $.0295

        Account's A and B both use a TB each.  If they were not consolidated billing.  Everything would be under the $.03 umbrella.  But with consolidated billing, all resources are aggregated and THEN charged.  So 2GB would be charged under one umbrella.