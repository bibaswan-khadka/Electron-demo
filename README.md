# Electron-starter-pack
This is a extended starterpack that includes Electron support so that you can deploy your webapps as desktop applications. To use the starterpack to make desktop applications.  

1. First clone it.  
2. Then call `yarn install` to install all dependencies. 
3. Then call `yarn build`. This will create a dist folder, which we will package into an app. 
4. Then call `yarn package-mac` or `yarn package-win`. This starterpack was created on mac so windows users might need to change some bash commands to their windows equivalents. It is recommended for windows users to make the electron starterpack using their own already existing starterpacks with the instructions below to prevent these issues. 
5. After calling the package command you will see a folder called release builds. That is where you will find your packaged app. Navigate to the app using the file manager and open it to use. 

## Instructions for adding electron support to your already existing starterpack or webapp

1. First clone your starterpack to your terminal from github. 
2. Call `yarn install` to install packages. 
3. Now lets add electron. Call `yarn add electron` 
4. We are also going to add some other things. Call `yarn add wait-on concurrently --dev` to add concurrent support. This will allow us to open our app on localhost and in electron at the same time and let us edit our app when in development mode. 
5. Next call `yarn add electron-is-dev` to allow us to detect when we are in dev mode vs production mode. 
6. Now let us create the electron winodow. First create a folder called public with `mkdir public`at the root of your project. In here create a file called electron.js with `code electron.js`
7. Now open up `electron.js` and paste the following code in there. 
```javascript 
const electron = require('electron');

const { app } = electron;
const { BrowserWindow } = electron;

const path = require('path');
const url = require('url');
const isDev = require('electron-is-dev');

let mainWindow;

function createWindow() {
  mainWindow = new BrowserWindow({ width: 900, height: 680 });
  mainWindow.loadURL(isDev ? 'http://localhost:8080' : `file://${path.join(__dirname, '../dist/index.html')}`);
  mainWindow.on('closed', () => mainWindow = null);
}

app.on('ready', createWindow);

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
});

app.on('activate', () => {
  if (mainWindow === null) {
    createWindow();
  }
});
```  
This code is preety simple. See the video for more explanation. The code first imports electron, isDev detection and other   necessary things. We then create a mainWindow variable. We then create a function createWindow() to create our desktop app window. We then tell the window what to load. When in dev mode we load the localhost but when in production we load from the dist folder. The next two functions quit the app and create the winodw when the app is first opened.    

8. Now we need to make some changes to our package.json. Open up package.json and replace what was previously in main with `"main": "public/electron.js",` By doing this we are telling the computer to open up electron.js instead of the previous file on runtime.  
9. Now lets add a command to run development mode. Go to scripts and add ` "electron-dev": "concurrently \"BROWSER=none yarn start\" \"wait-on http://localhost:8080 && electron .\"", ` . This command will first open up your app on localhost, then wait and when that is finished it will open up the app in your electron window. In dev mode any changes you make in the local host will appear in the electron window as well. You can also debug straight in electron by opening up the dev tools in the electron tools by scrolling up to view and toggle developer tools.   
10. To run your app in dev mode call `yarn electron-dev`.  
11. Great now we have our electron app running. What about when we are ready to package and ship our app.   
12. To do this we need to do some more things. First add `"homepage": "./",` to your package.json. This will let the packager know where the root of your folder is.   <br> 13. Now lets add the packager. There are several different electron packagers just like there are different websites you can host you projects on. For this tutorial we will be using electron-packager. Call `yarn global add electron-packager --dev` to add the packager.   
14. Now go to your package.json file and add in the scripts section 
```
"package-mac": "electron-packager . --overwrite --platform=darwin --arch=x64 --icon=assets/icons/mac/icon.icns --prune=true --out=release-builds",
"package-win": "electron-packager . electron-tutorial-app --overwrite --asar=true --platform=win32 --arch=ia32 --prune=true --out=release-builds --version-string.CompanyName=CE --version-string.FileDescription=CE --version-string.ProductName=\"Electron Tutorial App\"",
"package-linux": "electron-packager . electron-tutorial-app --overwrite --asar=true --platform=linux --arch=x64 --prune=true --out=release-builds"
    
```
These commands will add packaging support for mac, windows and linux. Amazing huh, that with a simple click and without having to know anything about those systems you can deploy to all three if you want.   
15. **(Optional but recommended)** If you choose to skip this step remove the `--icons` option flag from the package command we added in scripts. Lets add a icon for our desktop app. By default if you do not specify it you will get the electron logo. To add the logo create a directory called assets at the root. Then create a folder called icons inside it. And then create file named mac inside that (This would be anything else for other systems). In the mac folder add the logo. Macs use icns files for logos. Navigate to the `assets/icons/mac` folder in this repo to download the logo. It's a smiley face. To add a logo add `--icon=assets/icons/mac/icon.icns` or whatever else your path is after the `--arch` flag. Be aware that other systems use different file formats for logos. For windows it is .ico and linux it is .png . Here are some good sites for icons https://www.iconfinder.com/ and https://icon-icons.com/ .  
16.  Now lets package the app. First call `yarn build`. This will create the dist folder, from which we will package up the files. 
17. Then call `yarn package-mac` or the respective command for your system.  
18. And just like that you have created your desktop app. You can find the app in the release builds folder. Navigate to it using your file explorer and open up your desktop app.  
19. You can use these same steps to package any one of your webapps as a desktop app. 
