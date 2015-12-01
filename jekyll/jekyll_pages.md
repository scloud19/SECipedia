Key Takeaways
	Extract common content (headers,footers,etc) into a layout for reuse.

	Utilize a directory structure where each page's index.html lives in its own descriptive DIR.

Pages
	Pages are static HTML pages
		Ex: index.html, about.html, etc.
	
		You can still use layouts and/or includes in these files

		Page directory structure for clean URLS
			EX:
				.
				├── _config.yml
				├── _includes/
				├── _layouts/
				├── _posts/
				├── _site/
				├── about/
				|   └── index.html  # => http://example.com/about/
				├── contact/
				|   └── index.html  # => http://example.com/contact/
				└── index.html      # => http://example.com/






