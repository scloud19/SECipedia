For my use case, I stopped documenting right above
	http://jekyllrb.com/docs/plugins/#generators

Key Takeaways
	Github pages are generated using the --safe option in jekyll (so plugins are disabled)
		You can still utilize Github Pages to publish your site, but you need to convert the site locally and then push the generating static files to your Github repo (and not the Jekyll source files.)

3 ways to install plugins (Not mutually exclusive)
	_plugins
		In your site source root, make a _plugins directory and place everything here.

		Any file ending in .rb will be loaded before Jekyll generates your site

	_config.yml
		Add a new array with the key 'gems'
			Ex:
			gems: [jekyll-coffeescript, jekyll-watch, jekyll-assets]
			# This will require each of these gems automatically.

			// Make sure to install the gems
			gem install jekyll-coffeescript jekyll-watch jekyll-assets

	Add in the plugins to a bundler group in the Gemfile
		Ex: 
		
			group :jekyll_plugins do
   				gem "my-jekyll-plugin"
   				gem "another-jekyll-plugin"
 			end

 		// bundle install

