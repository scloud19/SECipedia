 Key Takeaways
 	If deploying to Github Pages, you can't utilize latent semantic indexing.


 Variables
 	Any files with YAML front matter will have access to variables via Liquid

 	Global Variables
 		site
 			sitewide meta-data + _config.yml settings
 		page
 			page specific information + the YAML front matter.

 			Ex: If you set a custom variable in the front matter, you can access it through here.

 		content
 			In layout files
 				The content of the Post/Page that's being injected
 			This isn't available in the Post/Page file itself.

 		paginator
 			Used if the pagination config option is set

 		site.time
 			The time (when you run the jekyll CLI)

 		site.pages
 			A list of all pages

 			Subset
				site.html_pages
					Only contain HTML files

 		site.posts
 			Reverse chronological list of all posts

 		site.related_posts
 			Utilized for related posts using latent semantic indexing (this can't be utilized if deploying to Github pages).

 		site.static_files
 			A list of all static files
 				Files not processed by Jekyll's converters or Liquid renderer.

 				Each file has these properties
 					path
 					modified_time
 					extname

 				For more variables, look at the jekyll_static_files.md

 			Subset
 				site.html_files
 					Only contains files that end in .html

 		site.collections
 			A list of all of the collections

 		site.data
 			A list containing the data loaded from the YAML files located in _data DIR

 		site.documents
 			A list of all of the documents in every collection

 		site.categories.CATEGORY
 			All list of all Posts in category.

 		site.tags.TAG
 			The list of all Posts with TAG


 		site.[CONFIG_DATA]
 			All the variables set via the CLI and your _config.yml are available through the site variable.

 			Ex:
 				// url: http://www.zachsite.com
 				// in your config file

 				In your posts/pages you can access via
 				site.url
	
	Page Variables
		page.content
		page.title
		page.excerpt
			un-rendered excerpt of the page
		page.url
			Ex: /2008/12/14/my-post.html
		page.date
		page.id
			A unique identifier to the post
		page.categories
			The categories that this post belongs to

			This is derived from the directory structure above the _posts directory

			Ex: /work/code/_posts/2008-12-24-closures.md

			Would have this variable set to
				['work', 'code']

			This can also be set in the YAML Front Matter

		page.tags

		page.path
			The path to the raw post or page

		page.next
			The next post relative to the position of the current post in site.posts

			Returns nil for the last entry

		page.previous
			Similar logic as above

		page.YOUR_CUSTOM_FRONT_MATTER
			Any custom front matter that you specify

			Ex: custom_css: true

				// Can be retrieved by
				// page.custom_css

	Paginator
		This variable is only available on index.html files
		
		paginator.per_page
			number of posts per page

		paginator.posts
			Posts available for that page

		paginator.total_posts
			Total number of Posts

		paginator.total_pages
			Total number of pages

		paginator.page
			The number of the current page

		paginator.previous_page
			The number of the previous page

		paginator.previous_page_path
			The path to the previous page

		paginator.next_page
		paginator.next_page_path
			Similar logic as above

