Key Takeaways
	Front Matter block must be the first thing in the file for it to be processes.
		You don't have to specify any variables, etc.

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

			category

