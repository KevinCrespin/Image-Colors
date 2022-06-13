---
title: "Gopher"
slug: "gopher"
hidden: false
createdAt: "2022-02-18T18:45:16.979Z"
updatedAt: "2022-06-13T17:07:03.331Z"
---
[block:callout]
{
  "type": "info",
  "title": "What does this do?",
  "body": "Responsible for New Search and DMOs, as well as Gopher API used for the Gopher Extension."
}
[/block]

[block:callout]
{
  "type": "info",
  "body": "Make sure you've run through the steps in [General Development Environment Set Up](doc:setting-up-the-development-environment).\n\nEnsure your Ruby and Rails versions match what is specified in the section below.\n\nEnsure you have the project master.key",
  "title": "Getting Started"
}
[/block]

[block:api-header]
{
  "title": "Versions"
}
[/block]
- Ruby 2.6.1
- Ruby on Rails 6.1.3
[block:api-header]
{
  "title": "1. Install Dependencies"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "# To install Redis\nbrew install redis\n\n# To install memcached\nbrew install memcached\n\n# To install AnyCable-Go\nbrew install anycable-go\n\n# To install ImageMagik\nbrew install ImageMagick\n\n# To install ElasticSearch \nbrew tap AdoptOpenJDK/openjdk\nbrew cask install adoptopenjdk12\necho export \"JAVA_HOME=\\$(/usr/libexec/java_home)\" >> ~/.zshrc\nsudo bin/elasticsearch install\n\n# To install Kibana\ncurl -O https://artifacts.elastic.co/downloads/kibana/kibana-7.9.3-darwin-x86_64.tar.gz\ncurl https://artifacts.elastic.co/downloads/kibana/kibana-7.9.3-darwin-x86_64.tar.gz.sha512 | shasum -a 512 -c - \ntar -xzf kibana-7.9.3-darwin-x86_64.tar.gz\ncd kibana-7.9.3-darwin-x86_64/\n./bin/kibana\n# You can now visit kibana at http://localhost:5601/app/dev_tools\n\n# To install foreman\ngem install foreman\n\n# To install nginx\nbrew install nginx\nmkdir log/nginx\n\n# To install node\nbrew install node\n\n# To install yarn \nbrew install yarn\nyarn install --check-files",
      "language": "shell",
      "name": "Terminal"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "warning",
  "title": "",
  "body": "Getting stuck? Suggest an Edit to these instructions."
}
[/block]

[block:api-header]
{
  "title": "2. Install gems"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "# Install gems\nbundle install\n\n# If your bundle install fails due to puma intallation failing\ngem install puma:4.3.3 -- --with-cflags=\"-Wno-error=implicit-function-declaration\"\n\n# If your bundle install fails doe to thin installation failing\ngem install thin -v '1.7.2' -- --with-cflags=\"-Wno-error=implicit-function-declaration\"",
      "language": "shell",
      "name": "Terminal"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "warning",
  "body": "If you get hit with git authentication errors during this process, you will need to create and use a personal access token. See instructions [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token). Simply use this token when prompted for a password when running `bundle install`."
}
[/block]

[block:api-header]
{
  "title": "3. Enviroment files set up"
}
[/block]
Make sure you have the project's master.key file under `_config/master.key`

Create a .env file in the project root directory and add the following:
[block:code]
{
  "codes": [
    {
      "code": "ANYCABLE_REDIS_URL=redis://localhost:6379/5\nANYCABLE_RPC_HOST=[::]:50051\nSIDEKIQ_REDIS_URL=redis://localhost:6379/0\nPROPERTY_SEARCH_INDEX=properties\nGOPHER_BASE_URL=https://joingopher.net:3000\nCABLE_URL=wss://joingopher.net:3334/cable\nNODE_ENV=development\nTYPEAHEAD_INDEX=pelias\nBIG_QUERY_ENV=\"development\"",
      "language": "json",
      "name": ".env"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "4. NGINX Permissions"
}
[/block]

In the development_nginx.conf.erb file, replace `your_user_name` with the output of the following command:
[block:code]
{
  "codes": [
    {
      "code": "id -un",
      "language": "shell",
      "name": "Terminal"
    }
  ]
}
[/block]
Then replace `your_group_name` with the output of this command:
[block:code]
{
  "codes": [
    {
      "code": "id -g -n $whoami",
      "language": "shell",
      "name": "Terminal"
    }
  ]
}
[/block]
Uncomment the `user your_user_name your_group_name;` line by removing the `#` sign once you've filled in those fields
[block:api-header]
{
  "title": "5. Starting The Server"
}
[/block]
To run the server run the following command:
[block:code]
{
  "codes": [
    {
      "code": "foreman start",
      "language": "text",
      "name": "Terminal"
    }
  ]
}
[/block]
The web application should be accessible from [https://joingopher.net/](https://joingopher.net/)
[block:callout]
{
  "type": "warning",
  "title": "Troubleshooting",
  "body": "Common errors while launching the server: \n\n1. `java.nio.file.AccessDeniedException`\n- Run: `sudo chmod -R 777 ~/.evm`\n2. `java.lang.IllegalStateException: failed to obtain node locks`\n- Run:`ps aux | grep 'java'` to find what processes are running java along with their PID's\n- Run: `kill -9 <PID>` with the PID of the process running elasticsearch to kill it off\n3. If you are not able to run nginx on ports lower than 1000, try adding this to your local development.rake file:\n- Add: `system \"sudo nginx -c #{conf_path}\"`\n4. If this error pops up: `nginx: [emerg] bind() to 0.0.0.0:443 failed (48: Address already in use) & nginx: [emerg] bind() to 0.0.0.0:80 failed (48: Address already in use)`\n- Run: `sudo killall nginx`"
}
[/block]

[block:api-header]
{
  "title": "6. Set up Search Pages"
}
[/block]
In order to access New Search and DMOs pages, we have to first index SearchPages and GopherProperties, and also upload assets to each SearchPage. 

**  Indexing SearchPages and GopherProperties**

Make sure you have set up your database and it's up to date. To verify that, run the following:
[block:code]
{
  "codes": [
    {
      "code": "# Run a rails console\nrails console\n\n# Verify the latest SearchPage record\nSearchPage.last\n\n# Verify the latest GopherProperty record\nGopherProperty.last",
      "language": "ruby",
      "name": "Terminal"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "warning",
  "body": "If the commands above don't yield a SearchPage and GopherProperty record, please go to theguestbook project and get a copy of the production database via a database dump by running `./prod-dump.sh` and following the instructions. If this is the first time running this script please ask a developer about the process."
}
[/block]
If everything looks good. Just make sure gopher is running locally with the `foreman start` command, then open up the rails console and run the following:
[block:code]
{
  "codes": [
    {
      "code": "SearchPage.__elasticsearch__.create_index! force: true\nSearchPage.__elasticsearch__.import",
      "language": "shell",
      "name": "Terminal"
    }
  ]
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "GopherProperty.__elasticsearch__.create_index! force: true\nGopherProperty.property_import",
      "language": "shell",
      "name": "Terminal"
    }
  ]
}
[/block]
** Accessing Gopher Admin to update SearchPages assets**

To access New Search and DMOs, we need to upload assets to each SearchPage record, we do this with [Gopher Admin](http://joingopher.net/admin). In order to access it locally, you will need to modify the file `gopher/app/controllers/admin/application_controller.rb`. Find the `authenticate_admin` method and comment out the body of the function, then add this line above the method declaration. Your file should be modified like the following:
[block:code]
{
  "codes": [
    {
      "code": "# FROM\ndef authenticate_admin\n  unless current_gopher_user&.dhisco_content_admin?\n    flash[:alert] = ‘Must be signed in as an admin to continue.’\n    redirect_to root_path\n end\nend\n\n# TO\nskip_before_action :verify_authenticity_token\ndef authenticate_admin\n  # unless current_gopher_user&.dhisco_content_admin?\n  #   flash[:alert] = ‘Must be signed in as an admin to continue.’\n  #   redirect_to root_path\n  # end\nend",
      "language": "ruby",
      "name": "application_controller.rb"
    }
  ]
}
[/block]
You should now be able to access [joingopher.net/admin](http://joingopher.net/admin).

For editing/accessing text info in gopher admin the application_controller.rb changes are sufficient, but for uploading assets you need different permission for NGINX. 

Within the `lib/tasks/development.rake` file, append `sudo` to the beginning of the two nginx commands. Your file should match to the following:
[block:code]
{
  "codes": [
    {
      "code": "# FROM\n    system \"sudo nginx -c #{conf_path}\"\n    system \"sudo nginx -t -c #{conf_path}\"\n \n# TO\n    system \"sudo nginx -c #{conf_path}\"\n    system \"sudo nginx -t -c #{conf_path}\"",
      "language": "ruby",
      "name": "development.rake"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "warning",
  "body": "Restart your server!"
}
[/block]

Now navigate to Gopher Admin > SearchPages and click "Edit Pages." Grab the assets from another developer, then upload the respective favicon, logo, and header image for each SearchPage. This includes The Guestbook (New Search), Cook Islands, Visit Bakersfield, and Saint Lucia. (You can ignore other SearchPage)
[block:callout]
{
  "type": "info",
  "body": "This process is only required once, but if you drop your database you will have to repeat this step!"
}
[/block]

[block:callout]
{
  "type": "danger",
  "body": "Make sure to revert the changes you made on ` lib/tasks/development.rake` and `app/controllers/admin/application_controller.rb` since we DO NOT commit these changes!",
  "title": ""
}
[/block]

[block:api-header]
{
  "title": "7. Rate Shop set up"
}
[/block]
If you are developing for Rate Shop you will need two more projects `direct_rate_apis` and `remote_browser`.

**Direct Rates APIs set up**
[block:code]
{
  "codes": [
    {
      "code": "# Make sure you clone this repository on your Projects folder\ngit clone https://github.com/universalpoints/direct_rate_apis",
      "language": "text",
      "name": "Terminal"
    }
  ]
}
[/block]
Then locate the Gemfile and update the following content and make sure that [CURRENT_VERSION] and [YOUR PROJECTS LOCATION] are correct. You can check the Direct Rates APIs' current version [here](https://github.com/universalpoints/direct_rate_apis/releases).
[block:code]
{
  "codes": [
    {
      "code": "# FROM\n  # If using username/pw for login\n  gem 'direct_rate_apis',  git: 'https://github.com/universalpoints/direct_rate_apis', tag: '[CURRENT_VERSION]'\n  # If using SSH key\n  # gem 'direct_rate_apis',  git: 'git@github.com:universalpoints/direct_rate_apis', tag: '[CURRENT_VERSION]'\n  # If using local\n  # gem 'direct_rate_apis', path: '[YOUR PROJECTS LOCATION]/Projects/direct_rate_apis', tag: '[CURRENT_VERSION]'\n\n# TO\n  # If using username/pw for login\n  # gem 'direct_rate_apis',  git: 'https://github.com/universalpoints/direct_rate_apis', tag: '[CURRENT_VERSION]'\n  # If using SSH key\n  # gem 'direct_rate_apis',  git: 'git@github.com:universalpoints/direct_rate_apis', tag: '[CURRENT_VERSION]'\n  # If using local\n  gem 'direct_rate_apis', path: '[YOUR PROJECTS LOCATION]/Projects/direct_rate_apis', tag: '[CURRENT_VERSION]'\n",
      "language": "ruby",
      "name": "Gemfile"
    }
  ]
}
[/block]
Then make sure you install the gem. You might have to restart the server to see your changes.
[block:code]
{
  "codes": [
    {
      "code": "bundle install",
      "language": "shell",
      "name": "Terminal"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "danger",
  "body": "This will swap the Rate Shop gem to your local files. We do not commit these changes! \nMake sure to follow the Rate Shop documentation for more information."
}
[/block]
**Web Crawling set up**

Gopher uses Capybara with Selenium to crawl to hotel sites to scrape rates. To set up this locally we have dockerized this setup.

Please go to the official Docker site and [download the latest version](https://docs.docker.com/get-docker/).

Then clone the Remote Browser repository on your projects folder.
[block:code]
{
  "codes": [
    {
      "code": "# Make sure you clone this repository on your Projects folder\ngit clone https://github.com/universalpoints/remote_browser",
      "language": "text"
    }
  ]
}
[/block]
To run Selenium Grid and each Node please run Docker and then run the following command on the terminal:
[block:code]
{
  "codes": [
    {
      "code": "# For MacOS x86_64 architecture\ndocker-compose -f docker-compose.yml up\n\n# For MacOS arm64 architecture\ndocker-compose -f arm-docker-compose.yml up",
      "language": "text",
      "name": "Terminal"
    }
  ]
}
[/block]
To check if Selenium Grid and the Nodes are running visit [http://localhost:4444/grid/console](http://localhost:4444/grid/console) for x86_64 architecture machines and [http://localhost:4444/ui/index](http://localhost:4444/ui/index) for arm64 architecture machines. You should see Selenium 3 Grid for x86_64 architecture machines and Selenium 4 Grid for arm64 architecture machines both should have 2 Nodes running, one with Chrome browser and one with Firefox browsers.
[block:api-header]
{
  "title": "8. Gopher Extension set up"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "# Make sure you clone this repository on your Projects folder\ngit clone https://github.com/universalpoints/gopher-extension\n\ncd gopher-extension\nnpm install",
      "language": "text",
      "name": "Terminal"
    }
  ]
}
[/block]
** To build a Chrome extension locally **
[block:code]
{
  "codes": [
    {
      "code": "./node_modules/gulp/bin/gulp.js buildChrome --env=development",
      "language": "text",
      "name": "Terminal"
    }
  ]
}
[/block]
1. Go to chrome://extensions
2. Click "Load Unpacked"
3. Load /build/gopher-chrome-extension

<br>

** To build a Safari extension locally **
[block:code]
{
  "codes": [
    {
      "code": "# Install nvm\ncurl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash\nexport NVM_DIR=\"$([ -z \"${XDG_CONFIG_HOME-}\" ] && printf %s \"${HOME}/.nvm\" || printf %s \"${XDG_CONFIG_HOME}/nvm\")\"\n[ -s \"$NVM_DIR/nvm.sh\" ] && \\. \"$NVM_DIR/nvm.sh\"\n\n# Verfy nvm\ncommand -v nvm\nnvm install 14\n",
      "language": "text",
      "name": "Terminal"
    }
  ]
}
[/block]
Install [XCode](https://developer.apple.com/xcode/)

1. In order to get the signing certificates, you will need to have someone invite you to our Guestbook Team account.
    1. Have a teammate invite you using this [link](https://appstoreconnect.apple.com/access/users)
    2. Once you are invited, sign in to your account in Xcode. If you are having issues - please refer to the dev [FAQ](https://github.com/universalpoints/gopher-extension/wiki/Common-Issues-during-local-development) 
2. Build archive
    1. Set scheme to _GopherDebug_ in the top left of the Xcode window
    2. Clean the build directory (_Product_ -> _Clean Build Folder_)
    3. Archive and build (_Product_ -> _Archive_)
3. Find Organizer window (_Window_ -> _Organizer_)
4. Download the build
    1. Select the new archive
    2. Click _Distribute App_ in the top right corner of the Organizer
    3. Select _Development_ 
    4. Check _Automatically manage signing_ and click _next_
    5. Click _Export_ and save
5. Open the folder that was saved and click the _Gopher.app_ file. That will start the app
6. Make sure the [Gopher Server](https://github.com/universalpoints/gopher) is running so you can access api endpoints for rate crawling.
[block:api-header]
{
  "title": ""
}
[/block]
