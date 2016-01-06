List
  It is python's "array"
  A value that holds values (or a list's items)

  A list preserves its order

  list_variable[-1]
    This will equal the last index in the list, etc.
      In general, you can use negative indexes

  slice
    Has two index inside of it
    Ex:
      dude = ['man', 'theman', 'foo', 'g']

      dude[1:3]
        -> ['theman', 'foo']

        # Starts at index 1 and then goes up to (but not including) index 3

      returns a brand new list

      You can utilize slices in assignment statements

        dude = ['man', 'theman', 'foo']
        dude[1:3] = ['thewoMAN', 'sup', 'yo']              
        -> ['man', 'thewoMAN', 'sup', 'yo']
        

