MS = ModSecurity

Everything in ModSecurity revolves around two items
    Configurations
      How does Modsecurity process the data it sees?
    Rules
      What to do with the processed data

Rule composition
  Ex1: SecRule ARGS "<script>" log,deny,status:404
    ARGS "script"
      What we are looking for in the input data
    log,deny,status:404
      If we find this pattern, do this.

  RULE Syntax
    SecRule VARIABLES OPERATOR ACTIONS
      VARIABLES
        Tells MS where to look
        Ex: ARGS
          Look in all request params

      OPERATOR
        Tells MS how to look
          This can be a RegEx pattern

      ACTION
        What to do when a match is found
        Thus, in Ex1:
          log the problem, deny the transaction, use 404 for the denial.

