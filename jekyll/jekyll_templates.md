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

		





