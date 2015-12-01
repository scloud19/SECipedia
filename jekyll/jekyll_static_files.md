Static files
	In addition to renderable (pages) and convertible (posts) content, there's static files.

	Ex: images, PDFs, un-rendered content, or other files that don't include YAML front matter.

	Can be access in Liquid via "site.static_files"
		These files have the following variables

		file.path
			The relative path to the file
		file.modified_time

		file.extname
			Ex: '.jpg'