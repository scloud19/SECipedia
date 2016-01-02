> Gives the output

Instruction Types
  Expressions
    Are made of values and operators

    Always evaluate to a singe value

  Statement
    Don't evaluate to a single value
      Although, an assignment statement can have an expression as a part of itself


Booleans
  True, False
    Make sure to type them with an uppercase T/F

Comparison Operators
  0 == '0'
    False

  42.0 == 42
    True

    Thus, floats can be equal to ints

  0 and 0
    is equal to 0 && 0 in JS

  0 or 0
    is equal to 0 || 0 in JS    

  not True
    > False

    Same as JS's !


Strings
  'hello' * 3
    hellohellohello

  len(STRING_ARG)
    Gives the length

Comments
  # Single line comment

Type casting
  str()
  int()
  float()

  Ex:
    str( int( input() ) + 1)
      If enter 8
      > '9'

Truthy/Falsey
  bool(VALUE)
    To test truthy/falsey


  Strings
    Falsy
      ""
    All other values are truthy

  Ints/Floats
    Falsey
      0,0.0 (respectively)

    All other values are truthy


Flow Control Statements
  if
    if 'zach' == 'zach':
      print('Yo man!')

      # If it's true, it runs the indented code (inside the block)
        if not, it runs the unindented code (outside the block)

    Ex: if/else...

    name = 'zach'
    if name == 'zach':
      print('Yo Zach!')
    elif name == 'john'
      print('Yo John, I told you to scram!')
    else:
      print('Dude, get zach please!')

  while
    

Blocks
  Are sometimes called clauses

  Only start after a colon
    Ex:
    if True == True:
      print('Yo man!')

  All of the code that is within the same level of indentation

  Blocks begin when there's a new level  of increased indentation
  Blocks end when the indentation returns to its previous level
