 Key Takeaways

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

 		site.posts
 			Reverse chronological list of all posts

 		site.related_posts
 			