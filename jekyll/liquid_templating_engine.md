| strip_html
	Remove any html tags from output

	Ex: Stripping out <p> tags
	{{ post.excerpt | remove: '<p>' | remove: '</p>' }}
	