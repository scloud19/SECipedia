Permalinks

	Are constructed by creating a template URL

	Post Permalinks
		You can specify the permalinks for your site through the config file OR in the Front Matter for each post.
			Jekyll takes these configs and then generates a folder structure in your _sites

			Remember, the permalink configuation setting specifies the permalink style used for posts

		The default style is "date"
			EX: /:categories/:year/:month/:day/:title.html

		Template variables (aka available in Liquid)
			All of these variables are deduced from the posts filename
				year
				i_month
					Without the leading zeros
				i_day
				hour
					24 clock (00..23)
				minute
					(00..59)
				title
					Can be overriden via the document's 'slug' Front Matter
				categories
					Specified categories for the post.

					If a post has multiple categories, Jekyll will create a hierarchy
						Ex: /category1/category2

					 Jekyll automatically parses out double slashes in the URLs, so if no categories are present, it will ignore this.

	Page Permalinks
		Default style
			/:path/:basename

		The suffix style in the post permalink config file will be applied to pages as well.
			Ex: Trailing file EXT and/or "/"

	Extensionless permalinks
		Permalinks without a file EXT or trailing "/"
		
		Github Pages and Jekyll's built-in WEBrick server handle these files fine, but you might need to do additional configs on other servers.
			Ex: http://jekyllrb.com/docs/permalinks/#apache


