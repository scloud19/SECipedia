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

Functions
  All function calls return a value, even if it isn't explicitly stated
    If nothing is returned explicitly, a None value is returned

  def yoman(name_yo):
    print(name_yo + " is da man!")
    return name_yo

  None 
    Is the only value of the NoneType datatype

    A value that represents the lack of a value

  Keyword Arguments
    Is an argument that is preceded by an identifier (e.g. 'id=') in a function call or passed as a value in a dictionary preceded by **.

    Keyword arguments are usually for optional arguments that CAN be passed.

    Ex: In both dudeman() invocations, 3 and 5 are both keyword arguments

      dudeman(id=3, num=5)
      dudeman(**{'id': 3, 'imag': 5})

  Multiple Assignment

    Ex1: Putting each greetings' item in its own variable
      
      greetings = ['yo', 'whatup', 'holla!']

      greeting1, greeting2, greeting3 = greetings

      print(greeting1)
        -> 'yo'

    Ex2: Gets the same results at Ex1 
      greeting1, greeting2, greeting3 = 'yo', 'whatup', 'holla'      





  Function Scope
    Remember, the scope is just an area of the source code, but additionally it is a container of variables (i.e., global variable scope)
    Global Scope Variable
      Any variable that is assigned outside of a function

    Local Scope Variable
      A variable that's assigned within a function
        Exception:
          Remember, to access a global variable inside a local scope, you simply access it (don't utilize it in an assignment statement)

          However, to change a global scope's value inside a function..
            Ex:
              def change_da_man():
                global is_da_man
                is_da_man = 'Yes'

              is_da_man = 'No'
              change_da_man()
              print(is_da_man)
              -> 'Yes'


    Ex:
      dude_man = "isn't da man" # global variable

      def turn_into_da_man():
        dude_man = "is da man" # local variable

      turn_into_da_man()
      print(dude_man)
      -> "isn't da man"

    Rules:
      Code in a local scope can access global variables

      Code in one function's local scope can't use variables in another local scope
        Remember, once a function is returned, its local scope is destroyed. 



Flow Control Statements
  General: Flow Control 
    Execution will stop when the end of the program is encountered.
    However, if you want to exit prematurely:

    import sys
    sys.exit()


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

  for
    Ex:

    print('Count this, homie!')
    for i in range(5):
        print("Da number is " + str(i))


    range(5) will set i to 0,1,2,3,4
    
    In the for loop above, you could simply replace range(5) with [0,1,2,3,4] and it would do the same exact thing.


  while

    Ex:

    num = 0
    while num < 5:
      print(num)
      num = num + 1
      # cant do num++

  Looping keywords
    break

      Ex:

      name = ''    
      while True:
        print("What's your name, foo?")
        name = input()
        if name = 'zach':
          break

      print('Dang bruh, it has been a while')


    continue
      execution stops and returns to the top of the loop to reevaluate the condition

  Looping functions
    range
      returns a datatype called the range object

      Ex: range(4)
        -> range(0,4)

        A range object is a list like object
          Python sees range(0,4) to be similar to [0,1,2,3]

          list like objects in python are technically called sequences

      for i in range(5)
        On every iteration, i will increment from 0-4

      for i in range(5,10,x)
      On every iteration, i will increment from 5-9 in steps of X

        x
          is called the "step argument"

          If negative, it will count downwards 

    common looping patterns
      greetings = ['yo', 'sup', 'whatup']
      
      for i in range(len(greetings)):
        print('Index ' + str(i) + ' in greetings is: ' + greetings[i])






Blocks
  Are sometimes called clauses

  Only start after a colon
    Ex:
    if True == True:
      print('Yo man!')

  All of the code that is within the same level of indentation

  Blocks begin when there's a new level  of increased indentation
  Blocks end when the indentation returns to its previous level

Built-In Functions
  All python programs can call these

  These functions include: print(), input(), len()

  You don't have to use an import statement to get these functions

Standard Library
  A set of modules that python comes with.

  Each module is a python program that contains a group of related functions that can be used in your programs
    Ex: Math module has math functions


  You must import these modules with an 'import' statement
    Ex:
      import random, os, math
      random.randint(1,10)

      # You can import multiple modules in one import statement
      # Downside: You have to type random. for every function call, but this code is more readable relative to Ex 2.

    Ex 2:
      from random import *
      randint(1,10)

      # With this notation, you don't have to prepend the module name to the function call.

Third-Party Modules
  pip (pip3 on Mac)
    A CLI program used to install third-party modules

    Ex: pip3 install MODULE_NAME
