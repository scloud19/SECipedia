Operators
	For comparison operators don't use '===', only '=='
		Thus use, '!=', etc.

	'or' instead of ||
	'and' instead of &&

	contains
		checks the presence of a substring inside a string (or an array of strings)

		Ex:
			{% if product.title contains 'Pack' %}
			  This product's title contains the word Pack.
			{% endif %}

			{% if product.tags contains 'Hello' %}
			  This product has been tagged with 'Hello'.
			{% endif %}

