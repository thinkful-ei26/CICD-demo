# Travis and Heroku CICD setup in a nutshell

[Also available in a Gist](https://gist.github.com/cklanac/789e902b2f03963fabaf9032f35b19d7)

## Setup Continuous Integration with Travis CI

**Make sure your repo is ready to go**
On your local machine:
- Run `npm test` to ensure tests are working correctly locally
- Add properly configured `.travis.yml` (see below)
  ```yaml
  language: node_js
  node_js: node
  services:
  - mongodb
  ```
- Commit and push repo to Github

**Activate Travis integration on your repo**
On Travis - activate integration:
  - Go to Profile: User (in upper-right) > Accounts
  - Click "Sync Account" 
  - Activate repo 

On GitHub - verify integration and test:
- Go to Settings > Integrations & Services
   - There should be an entry for "Travis CI" under Services
   - Click the "edit" button, then click "Test service" to test integration

On Travis:
- Watch build complete successfully :-)

# Setup Continuous Deployment to Heroku

**Install Travis and Heroku CLIs**

Install Travis CI's CLI:
- [Official Instructions] https://github.com/travis-ci/travis.rb
- Short version: `gem install travis`

Install Heroku CLI:
- [Offical Instructions](https://devcenter.heroku.com/articles/heroku-cli)
- Mac: `brew install heroku`
- Win: [Download Installer](https://cli-assets.heroku.com/heroku-cli/channels/stable/heroku-cli-x64.exe)

> Problems installing? Try the [Heroku NPM package](https://www.npmjs.com/package/heroku-cli)
`npm install -g heroku-cli`

**On Command line**
Configure `.travis.yml` to deploy to Heroku:
  - Go to project:
    - Run: `CD <YOUR PROJECT>`
  - Login to Heroku
    - Run: `heroku login` (and enter your UN/PW)
  - Create an app
    - Run: `heroku create <app-name>`
  - Login to travis
    - Run: `travis login` (and enter your UN/PW for **GitHub**)
  - Add Heroku info to `.travis.yml`, run: 
    - Run: `travis setup heroku`
    - Follow prompts, make sure the app name and repo are correct
    - Your `.travis.yml` should look like this:
    ```yaml
      language: node_js
      node_js: node
      services:
      - mongodb
      deploy:
        provider: heroku
        api_key:
          secure: oOa1TMdgeY5+rySYW0HY30j+ot+KUqs1H...
        app: <APP-NAME-ON-HEROKU>
        on:
          repo: <GITHUB-USERNAME>/<REPO-NAME>
    ```
  - Ensure tests are still working
    - Run: `npm test` 
  - Commit and push changes to GitHub
    - Run  `git commit -am "setup CICD"`
  - Changes should deploy to GitHub > Travis CI > Heroku

**On Heroku**
Add mLab connection string to (Environment) Config Vars
  - Go to: Personal Apps > PROJECT NAME > Settings
  - Click "Reveal Config Vars"
  - Enter "DATABASE_URL" and your mlab connection string
    - `mongodb://<un>:<pw>@ds123456.mlab.com:23456/demodb`

**Extras** 
Add a Travis CI badge to your repo
  - On Travis CI, find your project and click on the badge
  - Change the dropdown menu to "Markdown" and copy the output
  - Add the badge code to your `readme.md` file, commit and push
  - The code looks something like this:
```md
[![Build Status](https://travis-ci.org/<USERNAME>/<REPO-NAME>.svg?branch=master)](https://travis-ci.org/<USERNAME>/<REPO-NAME>)```