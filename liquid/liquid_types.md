Types
	String
	Num
	Bool
		All values are truthy, except nil and false
	Nil
		An empty value that is returned when Liquid code has no results.

		It's not 'nill'

		It is falsy
	Array
		You can't initialize an array in Liquid
			You can access the array VIA liquid though

		Need to initialize via Javascript
			Ex:
				<script>
					var cars = ["Saab", "Volvo", "BMW"];
				</script>
	EmptyDrop
		Is an object that is returned whenever you to try access a non-existent object property

		empty?
			Is the only attribute on this object

			Is always "true"

			If something does exist, it will not have this attribute

		Ex: See if something exists before accessing any other attributes
		{% unless pages.frontpage.empty? %}
		  <h1>{{ pages.frontpage.title }}</h1>
		  <div>{{ pages.frontpage.content }}</div>
		{% endunless %}


