Data files
	In addition to the built-in variables that you can utilize, you can specify your own custom data that can be accessed via Liquid.
		This allows you to avoid repetition in templates (and other files)

		You can also means that you can set site specific options without having to utilize _config.yml

		Plugins/themes can utilize these files to set config variables

	Place your data files in _data directory
		Allowed types
			YAML
			JSON
			CSV
				CSV files must contain a header row

		The result will be available in
			site.data

	Ex: 
		//_data/members.csv
			name,github
			Tom Preston-Werner,mojombo
			Parker Moore,parkr
			Liu Fengyun,liufengyun

		// Render in template
			<ul>
			{% for member in site.data.members %}
			  <li>
			    <a href="https://github.com/{{ member.github }}">
			      {{ member.name }}
			    </a>
			  </li>
			{% endfor %}
			</ul>

	Sub-folders within _data
		Each folder level will be added to a variables namespace

		Ex:
			// _data/orgs/jekyll.yml

				username: jekyll
				name: Jekyll
				members:
				  - name: Tom Preston-Werner
				    github: mojombo

				  - name: Parker Moore
				    github: parkr

			// _data/orgs/doeorg.yml

				username: doeorg
				name: Doe Org
				members:
				  - name: John Doe
				    github: jdoe

			All organizations can now be accessed via 'site.data.orgs'
				Ex:
					<ul>
					{% for org_hash in site.data.orgs %}
					{% assign org = org_hash[1] %}
					  <li>
					    <a href="https://github.com/{{ org.username }}">
					      {{ org.name }}
					    </a>
					    ({{ org.members | size }} members)
					  </li>
					{% endfor %}
					</ul>

	Ex: Accessing a specific author
		Pages/Posts can access a certain data item.

		// _data/people.yml

			dave:
			    name: David Smith
			    twitter: DavidSilvaSmith

		// Inside a post

			---
			title: sample post
			author: dave
			---

			{% assign author = site.data.people[page.author] %}
			<a rel="author"
			  href="{{ author.twitter }}"
			  title="{{ author.name }}">
			    {{ author.name }}
			</a>
		





