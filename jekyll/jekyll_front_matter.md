Key Takeaways
	Front Matter block must be the first thing in the file for it to be processes.
		You don't have to specify any variables, etc.

	Probably want to organize your posts into categories/tags


General Info
	Any file that contains a YAML front matter block will be processed by Jekyll as a special file.

Format
	Front Matter block must be the first thing in the file and must take the form of valid YAML set between triple-dashed lines:
	EX:
		---
		layout: post
		title: Blogging Like a Hacker
		---

		The variables are OPTIONAL
	
	Front Matter Variables
		Between the dashed lines, you can set predefined variables or create custom ones of your own.

		Availability
			Can access using Liquid tags
				Both further down in the file
				In any layouts OR includes that the page/post relies on

		Predefined global variables that you can set
			layout
				Set to file name without the EXT

				specifies the layout file to use in _layouts

			permalink
				Site-wide default
					/year/month/day/title.html

					If you want something different
						Set this to the final URL
			published
				if false
					The post wont show up when the site is generated

			category/categories
				Instead of placing posts inside of folders, you can specify one or more categories that the post belongs to.

				Formatting
					categories
						Can be specified as a YAML list or comma separated string.
			tags
				Similar to categories, one/many tags can be added to a post

				Formatting
					Same as category/categories

			Predefined variables for post
				date
					This overrides the name of the post, which can ensure correct sorting.

					Format
						YYYY-MM-DD HH:MM:SS +/-TTTT; hours, minutes, seconds, and timezone offset are optional.


		Custom Variables
			Any variables that are input into the Front Matter that aren't predefined are 'custom variables'

			These variables are mixed into the data that is sent to the Liquid templating engine during compilation.

			Ex: If you set a 'title' custom variable, you can access it via
			{{ page.title }}



