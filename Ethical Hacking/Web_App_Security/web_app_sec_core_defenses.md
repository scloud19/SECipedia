Trust boundary
  There can't be a complete trust boundary between the internet's input into the server and the servers input into the application.  It is impossible to effectively sanitize everything at one layer (given the complexity of attacks).

    Boundary Validation
      A more effective model where each individual component of functional unit of the server-side application treats its input as coming from a potentially malicious source.
        Ex: WHH-27

        Sanitize based on the component (i.e. what the potential exploits can be for that functional unit)
        
        Data validation is performed at each of these trust boundaries, in addition to the external frontier between the client and server.
          It is essential to do it at every unique layer (as data from one layer can be mutated/transformed before reaching another layer)

          Do data validation both client side and server side.



Handling


