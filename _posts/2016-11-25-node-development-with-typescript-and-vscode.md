---
layout: post
title: "Use Typescript and VS Code to develop your NodeJS application"
description: ""
modified: 2016-11-25
tags: [node, typescript, vscode]
image:
  feature: abstract-10.jpg
comments: true
share: true  
---


<center>
  <br><img style="max-width: 100%;" src="{{site.baseurl}}/images/node_typescript/logos.png" /><br>
</center>

I'm using Typescript daily with angular since 6 months and see the positive impact on my workflow and the code I produce.
So I decided to use it on the Node side too, but it wasn't an easy task to have the setup I wanted!

I didn't find a place with all explanations up to date and using latest tools so I post it there to hopefully help some of you. ;)

Scroll down to find full JSON files details.

## A. Produce code compatible with Node:

NodeJS needs compatible JS files in input to work, so configure your <b>tsconfig.json</b> with these properties:

- `"target": "es6"` Or `"es5"` if you are using a Node version older than 6.
- `"module": "commonjs"` It tells compiler to convert all ES6 modules (`import`/`export`) to CommonJS modules (`require`/`module.exports`).


## B. Always lunch the app with transpiled JS files from latest TS files:

### ➜ VSCode automatically displays your TS error.

You don't need to run the Typescript compiler in watch mode.
Inside <b>tsconfig.json</b>, add property `"watch": false`.

<center>
  <br><img style="max-width: 100%;" src="{{site.baseurl}}/images/node_typescript/code1.png" /><br><br>
</center>

### ➜ Configure VSCode debugging. 

Use <b>lunch.json</b> config file for debugging session start and <b>tasks.json</b> to create a task which run Typescript compiler.

- Inside <b>lunch.json</b>, add property `"preLaunchTask": "tsc"` to force Typescript compilation before app start. 
- Inside <b>tasks.json</b>, add properties `"command": "tsc"` and `"isShellCommand": true` to make a task named <b>tsc</b> running Typescript compiler.
- Inside <b>tsconfig.json</b>, add property `"noEmitOnError": true` to make compiler throw error when something wrong. 

<br>Now when starting your app with <b>F5</b> (Or <b>Debug panel</b> > <b>Start Debugging</b>), if your TS files are not valid you will see the following message letting you stop debugging to correct it before lunch it again: 

<center><blockquote>Build error has been detected during preLaunchTask 'tsc'. DEBUG ANYWAY / CLOSE</blockquote></center>

<center>
  <br><img style="max-width: 100%;" src="{{site.baseurl}}/images/node_typescript/code2.png" /><br><br>
</center>


## C. Debug with breakpoints inside TS files:

### ➜ Add MAP files support.

- Inside <b>tsconfig.json</b>, add property `"sourceMap": true`.
- Inside <b>lunch.json</b>, add property `"sourceMaps": true`.

### ➜ Help VSCode find JS/MAP files linked to your TS files.

Having TS files and JS/MAP files in separate folders is a good practise.
To implement it:

- Inside <b>tsconfig.json</b>, add property `"outDir": "dist"`.
- Inside <b>lunch.json</b>, add property `"outDir": "${workspaceRoot}/dist"`

<center>
  <br><img style="max-width: 100%;" src="{{site.baseurl}}/images/node_typescript/code3.png" /><br><br>
</center>


## D. Use @typings/* to add external module definitions files:

- Run this command to install node definition file and lodash and express definition files:
`npm i --save-dev "@typings/node" "@typings/lodash" "@typings/express"`

It installs definitions files like any others node modules and Typescript compiler automatically check `/node_modules/@typings/lodash` when looking for definitions.

<center>
  <br><img style="max-width: 100%;" src="{{site.baseurl}}/images/node_typescript/code4.png" /><br><br>
</center>

Some node modules now include definitions files directly bundled with it (angular2 do it). 
[More details about it.](https://www.typescriptlang.org/docs/handbook/declaration-files/publishing.html)

Keep in mind that your <u>library</u> vs <u>library definition</u> versions could be different, it can leads you to "weird" cases like:

- Typescript say something is good but at execution it throws an error because wrong definition file!
- Typescript tells you this function doesn't exist in this module or with differents parameters but in really it does!

To avoid this, compare these 2 versions and don't hesitate to go throught your installed definition files.

For example, you can see all availables versions here:

- [lodash versions](https://libraries.io/npm/lodash/versions)
- [@types/lodash versions](https://libraries.io/npm/@types%2Flodash/versions)
<br><br>


## E. Know when use ES6 or CommonJS module syntax:

### ➜ ES6 module syntax (`import`/`export`)

- Use it between all your project TS files.
- Use it to import external modules having definitions files (node/express/lodash,.. in my case).

### ➜ CommonJS module syntax (`require`/`module.exports`)

- Use it only to import external modules without definitions files.
Typescript is ok with it because `require` definition is inside the node definition file we import from `@typings/node`.
	
It's weird to mix ES6 and CommonJS module syntax at first but keep in mind it will be always CommonJS modules inside your transpiled JS files.
<br><br>


## F. Make your custom types/interfaces definitions available everywhere:

In my humble opinion, import interfaces definitions in your TS files while they are here just to development workflow and disappear when transpiled to JS files is a bad practise.

So, to make it accessible from everywhere inside your project, here is my solution (maybe better ways but works fine for me):

Create a `/typings/index.d.ts` file and reference all your definitions files inside like this: 

{% highlight html %}
/// <reference path="tables.d.ts" /> 
/// <reference path="store/stateA.d.ts" />
/// <reference path="store/stateB.d.ts" />
{% endhighlight %}


# Config files sources:

### ➜ File "/package.json":

{% highlight json %}
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


### ➜ File "/tsconfig.config":

{% highlight json %}
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

### ➜ File "/.vscode/tasks.json":

{% highlight json %}
{
    "version": "0.1.0",
    "command": "tsc",
    "isShellCommand": true,
    "showOutput": "always"
}
{% endhighlight %}

### ➜ File "/.vscode/lunch.json":

{% highlight json %}
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
