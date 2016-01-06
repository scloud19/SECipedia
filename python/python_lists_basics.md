List
  It is python's "array"
  A value that holds values (or a list's items)

  A list preserves its order

  list('Yo!')
    -> ['Y','o','!']

    To get a list type from a range
      list(range(4))
        [0,1,2,3]


      Ex: Give me a list whose items start at 0 and go up to (but not including) 100.  Additionally, increment by two
        list(range(0, 100, 2))
        
  in operator
    See if a value is in a given list
      Ex: 'yo man!' in ['yo man!', 'yo woman!', 'yo dude!']
        -> True

  'not in' operator
    is the ! of in

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
          Defaults to the length of the list
            The end value IS included in the slice



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
        

  del statement
    used to delete items from a list

    Once the item is deleted, all of the necessary items are moved up; thus there's no gaps

    Ex: del list_variable[3]

  len(list)
    Will return the number of items

  In general, many of the things that you can do with strings, you can do with lists. For example...
    list concat
      [1,2] + [3,4]
      -> [1,2,3,4]

    list replication
      [1,2] * 2
      -> [1,2,1,2]
