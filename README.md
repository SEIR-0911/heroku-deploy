# Project Set Up and Deployment Guide

Make sure to set up your project following the steps listed in this document.

- [Scaffold Your Project](#scaffold-your-project)
- [Storing API Keys Outside of Github](#storing-api-keys-outside-of-github)
  - [Creating Variables in .env Files](#creating-variables-in-env-files)
  - [Using an Environment Variable in JavaScript](#using-an-environment-variable-in-javascript)
- [Deploying React Apps on Heroku](#deploying-react-apps-on-heroku)
  - [Setting Up Your Heroku Account](#setting-up-your-heroku-account)
  - [Configure Your Project on Heroku](#configure-your-project-on-heroku)
  - [Configuring API Keys on Heroku](#configuring-api-keys-on-heroku)
  - [Deploying Your App](#deploying-your-app)

## Scaffold Your Project

1. Change into your `sei/projects` directory with:

  ```bash
  cd ~/sei/projects
  ```

2. Run Create React App to scaffold your project by typing the follow command in the Terminal and replacing where it reads `your-project-name` with your project name.  Use kebab-case (all lowercase with hyphens separating words) and make sure there are **no** spaces or special characters in the project name you choose.

  ```bash
  npx create-react-app your-project-name
  ```

3. Change directories into the newly created project directory, replacing where it reads `your-project-name` with your project name:

  ```bash
  cd ~/sei/projects/your-project-name
  ```

4. Create a new file named `.env.local` (the name of this file must be exactly as written here) to store your API keys using the `touch` command in the Terminal:

  ```bash
  touch .env.local
  ```

4. Create your project's code repository on [Github](https://github.com/).

5. Once you've created your repository, copy the code in the section that reads **"…or push an existing repository from the command line"** and paste it in the Terminal.

## Storing API Keys Outside of Github

As developers, we need to guard against our API keys being pushed to Github.  One way to ensure that this doesn't happen is to store them in a separate file that is not tracked in Git at all.

The way we tell Git to ignore certain files is through a special file that is named `.gitignore`. This file must be placed in the root directory of the git repository.   Conveniently, create-react-app automatically creates this file for us and adds a handful of files to it that are commonly excluded from Git.  The `.env.local` file is one example of a file that is ignored by default.

To use this file to store your API keys, you create your variables in it and then reference them anywhere in your JavaScript files via a special object named `process.env`.

### Creating Variables in .env Files

To create a variable in a .env file, you will:

1. Open the file with `code .env.local`. If you didn't previously create this file, create it with `touch .env.local` first.
2. Add a variable to the file. The variable must be named with the prefix `REACT_APP_`. By convention environment variables are named in all caps using snake case (underscores between words).
3. The variable name is followed by an equal sign with no spaces before or after it.
4. After the equal sign, add the variable value.  Do not use quotes with .env variable values.  Spaces are permitted in the value.  The variable is read up to the first line break.
5. You can add as many variables as you like separated by a line break in the file.

For example, if you had an API key for a stock API that you want to use in a Create React App, you could add it like this:

```
REACT_APP_STOCK_API_KEY=4j4584fds930g5437d89
```

### Using an Environment Variable in JavaScript

When your app loads, the environment variables are loaded into memory.  This means that _if your server is already running_, you'll need to stop it and restart it after adding or changing an environment variable.

In your code you can then reference the variable using `process.env.REACT_APP_YOUR_KEY_NAME`.

For example, assume that the API you're using requires your API key to be added to your request query parameters like so:

```md
https://fakestocks.com/q?apikey=4j4584fds930g5437d89&symbol=AAPL
```

To substitute the hardcoded key in the example above with an API key in your `.env.local` file that you named `REACT_APP_STOCK_API_KEY`, you would do the following:

```js
// Use string concatenation or interpolation to add the key
const url = `https://fakestocks.com/q?apikey=${process.env.REACT_APP_STOCK_API_KEY}&symbol=AAPL`

// Use the url as normal in your request
axios.get(url)

```

## Deploying React Apps on Heroku

For this project, we'll be using Heroku to deploy our application.

### Setting Up Your Heroku Account

Before you can configure your project to run in Heroku, you'll need to create an account and install the Heroku CLI by following the steps below. This is a **one time setup** and will not need to be done for future projects.

1. Sign up for a free [Heroku account](https://signup.heroku.com/).

2. Install the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) (Command Line Interface) tool for your specific operating system:

  #### Mac OS

  ```bash
  brew tap heroku/brew && brew install heroku
  ```

  #### Ubuntu

  ```bash
  sudo snap install --classic heroku
  ```

3. Once the installation is complete and you're returned to the command prompt, make sure the install was successful by typing `heroku -v`.  This should result in seeing a heroku version displayed in the Terminal.

### Configure Your Project on Heroku

To configure a React application for Heroku, follow the steps below.  This initial set up process must be followed **only one time for your project**.

1. Login to Heroku from the cli by typing:

  ```bash
  heroku login
  ```

2. Make sure you are **in your local project directory** at the command prompt and if not, use `cd` to switch into your project directory.

  Next you'll create a project in Heroku.  The name of your project in Heroku must be **unique across all of Heroku**.  For this reason,  you may have to try several times before you find a name that is unique.  The project name in Heroku **does not** need to match your Github repository name or your local directory name.  Replace `myapp` below with your project name.

  ```bash
  heroku create myapp
  ```

  If the name is taken, you'll see a message like the following.   Just run the above command again with a different project name if this happens.

  ```bash
  Creating ⬢ myapp... !
   ▸    Name myapp is already taken
  ```

  You can also just type `heroku create` and Heroku will choose a random name for your project.

3. Run the following command in your project directory in Terminal to configure Heroku for React:

  ```bash
  heroku buildpacks:set mars/create-react-app
  ```

  You should get a message back in the Terminal that reads:

  ```bash
  Buildpack set. Next release on your-heroku-project-name will use mars/create-react-app.
  Run git push heroku master to create a new release using this buildpack.
  ```

### Configuring API Keys on Heroku

If you have an API key stored in a `.env.local` file, you must create a **config var** on Heroku so that the key is available to your deployed app. To do so, copy the entire API key/value pair in your `.env.local` file. Back in the Terminal, make sure you're in your project directory and type `heroku config:set ` and paste your API key that you copied.

For example, assuming that you had an API key set in your `.env.local` file named `REACT_APP_STOCK_API_KEY`, you would add it to Heroku as follows:

```bash
heroku config:set REACT_APP_STOCK_API_KEY=4j4584fds930g5437d89
```

### Deploying Your App

You will **continue to push your code to Github** as normal using:

```bash
git push origin main
```
>(If your main branch is called master, use `git push origin master`.) 


In addition, when you wish to deploy or update the deployed version of your app, **you will also separately push to Heroku**. The command to deploy or update is:

```bash
git push heroku main
```
>(If your main branch is called master, use `git push heroku master`.) 


Pushing to heroku will kick off all of the build processes needed to create and serve a production optimized version of your app.  This process can occasionally take up to 10 minutes to complete. Once it  is completed, you should see a message that reads:

```bash
remote: https://your-heroku-project-name.herokuapp.com/ deployed to Heroku
remote:
remote: Verifying deploy... done.
To https://git.heroku.com/your-heroku-project-name.git
 * [new branch]      main -> main
```

Use the URL that ends in `herokuapp.com` to visit your deployed app!
