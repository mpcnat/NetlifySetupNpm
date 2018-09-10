# How to Setup Netlify for Simple Angular based projects.

## Step 1. Edit package.json to include build scripts.

You'll need to edit your package.json file to include a script netlify can use to build your project so it can be served.

```javascript
  "scripts": {
    "ng": "ng",
    "webpack": "webpack",
    "start": "ng serve --host 0.0.0.0",
    "build": "npm install && ng build --prod",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
  },
```

In the above section of code, we edited the *build* property of *scripts* to include two commands.

1. npm install: to install all dependencies
2. ng build --prod: An angular cli command that instructs it to create a *dist* folder. This dist folder is the folder we will be getting netlify to display later.

## Step 2. Add Redirects

Angular sites are single page applications, which means that whatever you type in the url bar, it's *always* going to index.html, and the angular router will handle displaying whatever needs to be displayed.

When netlify deploys a site, this isn't the default behavour. When you type a url for a page on your web app, it will try and find the html file physically located at that location, resulting in a 404 as that page does not actually exist in an angular app.

So we need to add a redirects file to keep forcing the server to display index.html no matter what url is in the bar.

### 2.1 Create _redirects file

In the **src** folder of your angular application, add a _redirects file:

![create redirects file](https://github.com/GregCMills/NetlifySetupNpm/raw/master/images/redirects.png)

With the following content:

```
/*  /index.html 200
```

### 2.2 Reference _redirects file in .angular-cli.json

In your project's .angular-cli.json file, add *_redirects* to the asset property so that when your *dist* folder is created, angular knows to include the _redirects file.

This should look like this:

```javascript
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "project": {
    "name": "dashboard"
  },
  "apps": [
    {
      "root": "src",
      "outDir": "dist",
      "assets": [
        "assets",
        "favicon.ico",
        "_redirects"
      ],

```

## Step 3. Log in to netlify

Log in to to netlify, and when prompted enter in your gitlab credentials.
**NOTE** The gitlab account you use must be a **maintainer** of the repository. 'Developers' don't have the required permissions.

## Step 4. Choose Your Project and Branch.

Find the correct repository, and choose it:

![Choose Repository](https://github.com/GregCMills/NetlifySetupNpm/raw/master/images/chooseRepository.png)

Choose the branch you wish to be continously deployed:

![Choose Branch](https://raw.githubusercontent.com/GregCMills/NetlifySetupNpm/master/images/branch.png)

## Step 5. Write Build Command.

This is where we use the package.json build script, but there is one complication.

By default, netlify runs build commands from the root of your repository.

Our project repository structure was like this:
```
rowe
│   README.md
│   package-lock.json    
│
└───dashboard
│   │   src
│   │   package.json
│   │   [Everything Else]
│   │   
```
To use npm install, we need to be in the same directory as package.json (in our case, rowe/dashboard). 

So in addition to running the package.json build script via the command *npm run build* , we need to change directory. Our command was as follows, but this will need to be changed as per your own file structure:

![Build Command](https://github.com/GregCMills/NetlifySetupNpm/raw/master/images/build.png)

## Step 6. Write Publish Directory

As we want to publish the *dist* folder our package.json build script will generate, we need to tell netlify where this folder is located.

```
rowe
│   README.md
│   package-lock.json    
│
└───dashboard
│   │   **dist**
│   │   src
│   │   package.json
│   │   [Everything Else]
│   │   
```

Above was our own file structure, where **dist** was located in our dashboard folder. Our publish directory is therefore:

![Publish Directory](https://github.com/GregCMills/NetlifySetupNpm/raw/master/images/publish.png)

However, again you will need to adjust this for your own file structure.

## Step 7. Enjoy your continously deployed public site!

The final deploy settings should be something like this:

![Publish Directory](https://github.com/GregCMills/NetlifySetupNpm/raw/master/images/finalSettings.png)

If all goes to plan, when you deploy your site netlify will perform the following steps:

0. Do magic pre stuff.
1. Change to the directory that contains the package.json file.
2. Run npm install to get all dependencies.
3. Run ng build --prod to create your dist folder (which contains a _redirects)
4. Deploy site from '[something]/dist/'

And afterwards, it'll give you a link.

If not, send me a message on slack (@Greg Mills) and maybe I can help.
