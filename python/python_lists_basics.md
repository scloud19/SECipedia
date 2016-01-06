List
  It is python's "array"
  A value that holds values (or a list's items)

  A list preserves its order

  list_variable[-1]
    This will equal the last index in the list, etc.
      In general, you can use negative indexes

  slice
    Can have two indexes inside of it.
      ex: list[1:4]

      The first index is shown in the resulting sliced list
        The second index isn't shown, everything up to (but not including) the second index is sliced.

    Ex: dude = [1,2,3]
      dude[:1]
        Starting value:
          defaults to the beginning of the list
            -> [1]

      dude[0:]
        Ending value
          Defaults to the end of the list
            The end value IS included in the slice.



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
        

