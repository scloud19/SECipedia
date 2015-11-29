// These are my notes/clippings while going through the jekyll documentation

Key Takeaways
	_posts
		YOU MUST FOLLOW THE NAMING FORMAT
			YEAR-MONTH-DAY-title.MARKUP
	_site
		place is file in your .gitignore

A basic site looks like this
	.
	├── _config.yml
	├── _drafts
	|   ├── begin-with-the-crazy-ideas.textile
	|   └── on-simplicity-in-technology.markdown
	├── _includes
	|   ├── footer.html
	|   └── header.html
	├── _layouts
	|   ├── default.html
	|   └── post.html
	├── _posts
	|   ├── 2007-10-29-why-every-programmer-should-play-nethack.textile
	|   └── 2009-04-26-barcamp-boston-4-roundup.textile
	├── _data
	|   └── members.yml
	├── _site
	├── .jekyll-metadata
	└── index.html

File/Directory Description
	_config.yml
		Config data so you don't need to specify it on the CLI

	_drafts
		Unpublished posts

	_includes
		Partials that can be reused

		Ex:
		{% include footer.html}

		Will include the partial in
			_includes/footer.html

	_layouts
		Templates that wrap posts

		Chosen on a post-by-post basis in the YAML Front Matter

		{{ content }}
			Is used to inject content into the web page.

	_posts
		The dynamic content

		YOU MUST FOLLOW THE NAMING FORMAT
			YEAR-MONTH-DAY-title.MARKUP

		The permalinks can be customized for each post

	_data
		Well-formatted site data

		All .yml/.yaml/.json/.csv files in this directory will be available via site.data

		members.yml
			If this file exists in _data, then you can access contents of the file through site.data.members

	_site
		Where the generated site will be place

		TIP: Put this in your .gitignore

	*.html/*.md/*.markdown/*.textile
		Files in root directory (or not listed in the directories above)
			As long as the file has a YAML Front Matter section, it will be transformed by Jekyll.

	*.*
		If not spoken about above, these files will be copied verbatim to the generated site.
			Ex: css and images folders
				favicon.ico file, etc.

