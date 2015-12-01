Key Takeaways
	To create a post, simply put a file in the _posts directory with the format "YEAR-MONTH-DAY-title.MARKUP"

	post_url
		A tag that you can utilize to link to other posts without having to worry about the URL's breaking

	assets directory
		create this in the root folder

		used for any images/downloads/etc that are utilized in the site/posts

		Can make subfolders for pics, etc.

		Ex: Including an image asset in a post
		… which is shown in the screenshot below:
		![My helpful screenshot]({{ site.url }}/assets/screenshot.jpg)

		Ex: Linking to a PDF for readers to download
		… you can [get the PDF]({{ site.url }}/assets/mydoc.pdf) directly.

	code snippets
		Ex:
			{% highlight ruby linenos %}
			{% endhighlight %}

			linenos
				adds line numbers

Posts
	_posts
		Where your blog posts live
		
		These files are usually Markdown or HTML
		
		All posts must have YAML Front Matter
			Allows for conversion between their source format into an HTML page

	Creating posts
		All you need to do is create a new file in _posts

		These files have to have the format
			YEAR-MONTH-DAY-title.MARKUP
			EX: 2011-12-31-new-years-eve-is-awesome.md

	Creating an index/list of posts for the blog
		Ex: Create a list of links to your blog posts using Liquid templating language

			<ul>
			  {% for post in site.posts %}
			    <li>
			      <a href="{{ post.url }}">{{ post.title }}</a>
			      {{ post.excerpt }}
			    </li>
			  {% endfor %}
			</ul>

			// The excerpt is wrapped it p tags for you

		Excerpts
			excerpt_separator in _config.yml
				Can customize how the excerpts look

			Ex: Removing an excerpt's p tags
				{{ post.excerpt | remove: '<p>' | remove: '</p>' }}
