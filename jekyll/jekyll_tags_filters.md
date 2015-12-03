Key Takeaways
	When using liquid, you can always specify variables for filenames, etc.
		EX: {% include {{MY_VAR}} %}

Templates
	Jekyll supports all standard Liquid tags/filters

	Additional Filters from Jekyll
		
		Timing
			{{ site.time | date_to_long_string }}
				// 07 November 2008

		Where
			Select all objects in an array where the key has the given value
				{{ site.members | where:"graduation_year","2014" }}

		Group By
			Group an array's items by a given property
				{{ site.members | group_by:"graduation_year" }}

			Out: [{"name"=>"2013", "items"=>[...]},
				  {"name"=>"2014", "items"=>[...]}]

		URI Escape
			{{ "foo, bar \baz?" | uri_escape }}

			Out: foo,%20bar%20%5Cbaz?

		Number of words
			{{ page.content | number_of_words }}

			Out: 1337

		Array to sentence
			Useful for listing tags

			{{ page.tags | array_to_sentence_string }}

			foo, far, and faz

		Markdown to HTML
			{{ page.excerpt | markdownify }}

		Converting Sass/SCSS to CSS
			{{ some_scss | scssify }}
		    {{ some_sass | sassify }}

		Sort
			Sort an array

			Options for sorting via
				property name
				order (first or last)

			{{ page.tags | sort }}
			{{ site.posts | sort: 'author' }}
			{{ site.pages | sort: 'title', 'last' }}

		Making Slugs
			slugify
				Options (aka what to filter)
					none
						no characters
					raw
						spaces
					default
						spaces and non-alphanumeric characters
					pretty
						spaces and non-alphanumeric characters except for ._~!$&'()+,;=@
	Tags
		includes
			For reusing small page fragments within your site

			All includes must be placed in
				<source>/_includes/

			Ex: Using an include
				{% include footer.html %}

				OR

				{% include {{MY_VAR}} %}

			You can also pass parameters
				Literals have quotes, variables don't

				Ex: {% include footer.html param="value" variable-param=page.variable %}

					In the include, you can access the param via:
						{{ include.param }}






		





