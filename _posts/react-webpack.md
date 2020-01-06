---
layout:     post
title:      Easily set up a single page .NET Core application with Webpack and React in Visual Studio 2015
date:       2017-03-20
summary:    Details how to set up a .NET Core application with Webpack and React.
categories: 
---

![desk](/images/vs-webpack.png)

In 2017, setting up an environment for a modern web app can be heinous. With the rise of precompiled languages and [javascrip](https://hackernoon.com/tagged/javascript)t package managers galore, setting up a solid base for your project can take hours. Throw in trying to integrate this with a server-side framework like .NET Core, using Visual Studio as your IDE and boom — you’ve lost days.

Fortunately, thanks to the work of some githubbers on [aspnet-starter-kit](https://github.com/kriasoft/aspnet-starter-kit), you too can have a pretty mean set up for a single page .NET Core application with all the recently popular front-end goodies like React, Redux and Webpack.

Note that aspnet-starter-kit is built for Visual Studio Code. So, I have forked this great repository and made some minor tweaks, and I bring you the Visual Studio 2015 edition: [aspnet-starter-kit-vs-2015](https://github.com/codingupastorm/aspnet-starter-kit-vs-2015)!

Here’s what you need to do:

 1. Ensure you’re running [Visual Studio 2015 Update 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2015-update3-vs)

 2. Ensure you have the [.NET Core SDK](https://www.visualstudio.com/en-us/news/releasenotes/vs2015-update3-vs) installed

 3. Ensure Visual Studio is [configured to use Node 6 or higher](https://ryanhayes.net/synchronize-node-js-install-version-with-visual-studio-2015/)

 4. Install both NPM Task Runner and Webpack Task Runner in Visual Studio. In the menu, go to [Tools](https://hackernoon.com/tagged/tools)…Extensions and Updates, and search online for each of these

 5. Now you’re ready to get started! Clone [aspnet-starter-kit-vs-2015](https://github.com/codingupastorm/aspnet-starter-kit-vs-2015)

 6. Before you open the project in Visual Studio, navigate to the repository via the command line, and run *npm install *and wait for it to finish restoring packages

 7. Now you can open the project in Visual Studio, and open the Task Runner Explorer (View…Other Windows). In the task runner explorer, under Webpack, double click Run — Development. You should see a couple of green lines in the terminal

 8. You’re good to go! Hit play in Visual Studio and hopefully everything fires up.

There are a wealth of improvements that can be made to the repository — I’d like to set up hot reloading of code, and some transfer of data from the .NET Core app. Hopefully we can do that another time.