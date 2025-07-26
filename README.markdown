# Azure DevOps Pipelines CCMenu proxy

This is a quick and dirty proxy written in Ruby which you can use to wire up Azure DevOps build pipelines to [CCMenu](http://ccmenu.org/) on macOS.

## Installation

This assumes you are running macOS on Apple Silicon and clone your projects into `~/projects`. The included `launchd` script will make the proxy available at `localhost:39419`. Vary according to one’s taste. Pull Requests to make this more generic are welcome.

Install the proxy with the following:

```sh
cd ~/projects
git clone https://github.com/jasoncodes/azuredevops-ccmenu.git
mkdir -p ~/Library/LaunchAgents
ln -s ~/projects/azuredevops-ccmenu/launchd.plist ~/Library/LaunchAgents/azuredevops-ccmenu.plist
launchctl load ~/Library/LaunchAgents/azuredevops-ccmenu.plist
```

Test the proxy is running with `curl http://localhost:39419`. You should get a “Not Found” error if it’s running.

Next install Azure CLI via [Homebrew](https://brew.sh) if you haven’t already. This project uses Azure CLI to retrieve access tokens for Azure Pipelines.

```sh
brew install azure-cli
az login
```

## Configuration

Navigate to a build pipeline you want to monitor with CCMenu and you should end up at URL like this: `https://dev.azure.com/$ORG/$PROJECT/_build?definitionId=$DEFINITION`. Use values from this Azure Pipelines URL to make a CCMenu URL like this: `http://localhost:39419/build.xml?org=$ORG&project=$PROJECT&definition=$DEFINITION`

This CCMenu URL will default to displaying the status of the latest build on the pipeline, regardless of branch. When working on a team, you’ll most likely be more interested in the `main` branch and your current checked out branch. You can add `&branch=main` to return the status of the latest `main` build or `&checkout=my-project` for the currently checked out branch at `~/projects/my-project`.

Once you have the set of CCMenu URLs you should test them in your browser. Check you get an XML response for each URL showing the expected build information.

Finally, add these URLs to CCMenu. Open CCMenu's Pipelines list, click the plus in the toolbar, and select “Add project from CCTray feed…”. Paste your URL into the “Server” field and press return. CCMenu will retrieve the feed and populate the “Project” dropdown and “Display name” field. Tweak the “Display name” to your liking to uniquely identity each field, for example “my-project (main)” and “my-project (branch)”. Click “Apply” to add the feed to the list. Repeat for any other URLs.

It’s recommended that you enable the “with last build time” and “with last build label” preferences in CCMenu which will display details about the build (branch name and build number) in CCMenu’s dropdown.

## Licence

MIT
