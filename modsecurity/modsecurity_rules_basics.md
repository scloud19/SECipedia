Everything in ModSecurity revolves around two items
    Configurations
      How does Modsecurity process the data it sees?
    Rules
      What to do with the processed data

Rule composition
  Ex: SecRule ARGS "<script>" log,deny,status:404
    ARGS "script"
      What we are looking for in the input data
    log,deny,status:404
      If we find this pattern, do this.

    RULE Syntax
    SecRule VARIABLES OPERATOR ACTIONS
    VARIABLES

