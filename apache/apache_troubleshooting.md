Resource monitoring
  apache_key_modules.md/mod_status
    Can leverage this module as a starting place for problems related to resource utilization
      Ex: Mitigating a DoS attack
      
      Ex: A situation where the server is consuming all available resources (CPU/memory) and you wish to ID which clients/requests are causing this problem.
        Usually, through this process you can ID a web app that is misbehaving, or a certain client that's attacking your site.
          From here, you can potentially block a certain type of request/or a client.
        Process
          # top
            Identify the specific processes that are the main culprits.
              Order by CPU/memory memory usage
          Reload the server-status page, and look for these process ids
            You'll see what request is being served by that process
              As requests are transient, you might need to do this process a few times before you "catch it".

