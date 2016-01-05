Try/Except
  Ex: 
    def div_2_by(div_by):
      try:
        return 2/div_by
      except ZeroDivisionError:
        print('Error: You tried to divide by zero, FOO!')
      except:
        print('I catch all OTHER errors')

    Code execution goes into the try block
      If that fails, it goes to the correspondin error in the except clause.
        Once that's finished, it returns to whereever the code left off before the except block was ran.

  Good use:
    For input validation, if a user enters an input that is unexpected, you can catch it in the except block.
      Ex:
        print('How many kids on child support do you have?')
        
        try:
          childSupportKids = int( input() )
        except ValueError:
          print('Dang bro, just enter a number man!')
        
        # If you don't know the error type, you can force the error 
        # in the console and you'll see "ValueError: ..."

      
