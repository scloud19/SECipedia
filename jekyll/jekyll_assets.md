Assets
	Jekyll supports
		Sass
		Coffeescript
			via gem

	Must start file with 2 lines of triple dashes
		Ex:
			---
			---

			// start content
			.my-definition
			  font-size: 1.2em

	If you have a file named 'css/styles.scss'
		It will turn into 'css/styles.css'

	All Liquid filters/tags in asset files will be processed

	Sass/SCSS
		You can customize your Sass conversion in certain ways
			More info
				http://jekyllrb.com/docs/assets/#sassscss
				
			This includes pulling in from partials, etc.

			You can also utilize @import statements as well
