// These are my notes while going through the jekyll documentation

What is Jekyll?
	

jekyll build
# => The current folder will be generated into ./_site
	Use the _site file as a staging area.  Everything that is in this
	folder will be deleted on every rebuild.
		If you want to change this, utilize the <keep_files> configuration directive.

$ jekyll build --destination <destination>
# => The current folder will be generated into <destination>

$ jekyll build --source <source> --destination <destination>
# => The <source> folder will be generated into <destination>

$ jekyll build --watch
# => The current folder will be generated into ./_site,
#    watched for changes, and regenerated automatically.

$ jekyll serve --detach
# => A development server will run at http://localhost:4000/
	This will have automatic regeneration

	--detach
		allows us to keep this process in the BG

_config.yml
	Place this in the root of the source directory

	Options
		source: _source
		destination: _deploy
