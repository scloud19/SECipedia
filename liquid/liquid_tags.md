Tags
	Make up the programming logic that tells templates what to do

	Are always wrapped in {% %}

	Certain tags (for,cycle, etc.) can take on parameters.

	Ex:
		{% if user.name == 'zach' %}
		  Hey BRO!
		{% endif %}

	if/elsif/else

		{% if customer.name == 'kevin' %}
		  Hey Kevin!
		{% elsif customer.name == 'anonymous' %}
		  Hey Anonymous!
		{% else %}
		  Hi Stranger!
		{% endif %}

	case/when
		{% assign handle = 'cake' %}
		{% case handle %}
		  {% when 'cake' %}
		     This is a cake
		  {% when 'cookie' %}
		     This is a cookie
		  {% else %}
		     This is not a cake nor a cookie
		{% endcase %}

	for loop
		{% for product in collection.products %}
		    {{ product.title }}
		  {% endfor %}

		params
			limit
				Exits loop at specific index
				
				{% for item in array limit:2 %}
				  {{ item }}
				{% endfor %}

	break
		{% for i in (1..5) %}
		  {% if i == 4 %}
		    {% break %}
		  {% else %}
		    {{ i }}
		  {% endif %}
		{% endfor %}

		There is also a 'continue' tag as well

	
