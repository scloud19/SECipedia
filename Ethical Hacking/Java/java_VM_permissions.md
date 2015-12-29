Java Code
  Permission
    http://docstore.mik.ua/orelly/java-ent/security/ch05_02.htm
    
    A permission in java is a subclass of the Permission class


    Ex: Going off of our earlier discussion of socket permissions.
        public final SocketPermission extends Permission

        We utilize the subclass (SocketPermission), as our "permission mediator"
          We utilize SocketPermission

  Permission subclass scenarios
    Requesting a permission
    Granting a permission

    Pseudo code example
      A library will request a permission and a policy will grant/deny the request


