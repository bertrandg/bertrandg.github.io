---
layout: post
title: "Use Typescript and VS Code to developp your Node application"
description: ""
modified: 2016-11-25
tags: [node, typescript, vscode]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---


I'm using typescript daily with angular since 6 months and see the positive impact on my workflow and the code I produce.
So I decided to use it on the Node side too, but it wasn't an easy task to have the setup I wanted!

I didn't find a place with all explanations up to date and using latest tools so I post it there to hopefully help some of you. ;)

Scroll down to find full JSON files details.

## ➜ A. Produce code compatible with Node:

NodeJS needs compatible JS files in input to work, so configure your tsconfig.json with these properties:
 - `"target": "es6"` Or `"es5"` if you are using a Node version older than 6.
 - `"module": "commonjs"` It tells compiler to convert all ES6 modules(`import`/`export`) to CommonJS modules (`require`/`module.exports`).


## ➜ B. Always lunch the app with transpiled JS files from latest TS files:
 - VSCode automatically displays your TS error, you don't need to run the Typescript compiler in watch mode, in tsconfig.json, add property `"watch": false`.

<center>
  <br><img style="max-width: 100%;" src="{{site.baseurl}}/images/node_typescript/code1.png" /><br>
</center>

 - Use VSCode `lunch.json` config file to configure app debugging and `tasks.json` to create a task which run Typescript compiler.

Inside `lunch.json`, add property `"preLaunchTask": "tsc"` to force typescript compilation before app start. 
Inside `tasks.json`, add properties `"command": "tsc"` and `"isShellCommand": true` to make a task named 'tsc' running Typescript compiler.
Inside `tsconfig.json`, add property `"noEmitOnError": true` to make compiler throw error when something wrong. 

Now when starting your app with `F5` (Or `Debug panel` > `Start Debugging`), if your TS files are not valid you will see the following message letting you stop debugging to correct it before lunch it again: 
"Build error has been detected during preLaunchTask 'tsc-compiler'. DEBUG ANYWAY / CLOSE"

<center>
  <br><img style="max-width: 100%;" src="{{site.baseurl}}/images/node_typescript/code2.png" /><br>
</center>


## ➜ C. Debug with breakpoints inside TS files:
 - Add MAP files support inside `tsconfig.json` > `"sourceMap": true` and inside `lunch.json` > `"sourceMaps": true`.
 - Having TS files separated from your JS/MAP files is a good practise.

To implement it, inside `tsconfig.json` add property `"outDir": "dist"`.
And to make breakpoint works, inside `lunch.json` add property `"outDir": "${workspaceRoot}/dist"`

<center>
  <br><img style="max-width: 100%;" src="{{site.baseurl}}/images/node_typescript/code3.png" /><br>
</center>


## ➜ D. Use @typings/* to add external module definition files:
 - Run this command to install node definition file and lodash and express definition files `npm i --save-dev "@typings/node" "@typings/lodash" "@typings/express"`

Keep in mind that your "library" vs "library definition" versions is important, it can leads you to cases like:
 - Typescript say something is good but at execution it throws an error because wrong definition file!
 - Typescript tells you this function doesn't exist in this module or with others parameters but in really it does!

To avoid this, compare these 2 versions numbers (https://libraries.io/npm/@types%2Fnode/versions & https://libraries.io/npm/@types%2Flodash/versions) and don't hesitate to go throught you installed definition files.

<center>
  <br><img style="max-width: 100%;" src="{{site.baseurl}}/images/node_typescript/code4.png" /><br>
</center>


## ➜ E. Know when use ES6 import/export module syntax or CommonJS require syntax:
 - ES6 module syntax
	Use it between all your project TS files.
	Use it to import external modules having a definition file (node/express/lodash,.. in my case).
PHOTO4

 - CommonJS module syntax (`require`)
	Use it to import external modules without a definition file.
	Typescript is ok with it because `require` is inside the node definition file we import from `@typings/node`.
	
I know it's weird to mix ES6 and CommonJS module syntax at first but keep in mind it will be always CommonJS require inside your transpiled JS files.
	

## ➜ F. Make your custom interface definitions available everywhere in your project:
In my humble opinion, import interface definitions in your TS files while they are here just to development workflow and disappears when transpiled to JS files is a bad practise.

So, to make it accessible from everywhere inside your project, here is my solution (maybe better way but works fine for me):
Create `/typings/index.d.ts` and inside it, reference all your definition type files like this: 
{% highlight js %}
/// <reference path="defA.d.ts" /> 
/// <reference path="defB.d.ts" />
{% endhighlight %}


## ➜ Here is a github repository:

>>> github link typescript+node+express starter


### ➜ /PACKAGE.JSON

{% highlight js %}
{
  "name": "demo-project",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "bluebird": "^3.4.6",
    "body-parser": "~1.15.2",
    "cookie-parser": "~1.4.3",
    "debug": "~2.2.0",
    "express": "~4.14.0",
    "lodash": "^4.17.2",
    "mysql": "^2.12.0",
  },
  "devDependencies": {
    "@types/express": "^4.0.34",
    "@types/lodash": "^4.14.40",
    "@types/node": "^6.0.49"
  }
}
{% endhighlight %}

### ➜ /TSCONFIG.JSON

{% highlight js %}
{
  "compilerOptions": {
    "target": "es6",
    "module": "commonjs",
    "moduleResolution": "node",
    "sourceMap": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "removeComments": false,
    "strictNullChecks": true,
    "skipLibCheck": true,
    "noImplicitAny": false,
    "noEmitOnError": true,
    "watch": false,
    "outDir": "dist"
  },
  "exclude": [
    "node_modules"
  ]
}
{% endhighlight %}

### ➜ /.VSCODE/TASKS.JSON

{% highlight js %}
{
    "version": "0.1.0",
    "command": "tsc",
    "isShellCommand": true,
    "showOutput": "always"
}
{% endhighlight %}

### ➜ /.VSCODE/LUNCH.JSON

{% highlight js %}
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch program",
            "program": "${workspaceRoot}\\bin\\www",
            "cwd": "${workspaceRoot}",
            "outFiles": [],
            "sourceMaps": true,
            "outDir": "${workspaceRoot}/dist",
            "preLaunchTask": "tsc",
            "env": {
                "NODE_ENV": "development"
            }
        }
    ]
}
{% endhighlight %}





