# npm scripts Note



### What are NPM Scripts?

NPM scripts are, well, scripts. We use scripts to automate repetitive tasks. For example, 

- building your project, minifying Cascading Style Sheets (CSS) and JavaScript (JS) files. 
- Scripts are also used in deleting temporary files and folders, etc,.

There are many ways to pull this off — you could write bash/batch scripts, or use a task runner like Gulp or Grunt. However, a lot of people are moving over to NPM scripts for their simplicity and versatility. They also offer possibility of having fewer tools to learn, use, and keep track of.

### The Scripts Object in package.json

Most of our work will happen in the package.json file that NPM uses as a manifest of sorts. This is the file that is created when you run `npm init --yes`.

```
{
  "name": "test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

Notice the `scripts` object in the file. This is where our NPM scripts will go. NPM scripts are written as usual JSON key-value pairs where the key is the name of the script and the value contains the script you want to execute.

Here’s perhaps the most popular NPM script (and it’s also a special kind of script):

```
"scripts": {    "start": "node index.js",    ...}
```

You’ve probably seen this tons of times in your package.json files. And you probably know that you can type `npm start` to execute the script.

### Custom Scripts

The script we just saw is one of the “special” NPM scripts. You can execute it by simply typing `npm start`. These are scripts with names that NPM recognizes and attaches special meaning to. For example, you can write a script called `prepublish`. NPM will execute the script before your package is packed and published, and also when you run `npm install` locally without any arguments. More on such scripts [here](https://docs.npmjs.com/misc/scripts).

But NPM also let’s you define your own custom scripts. This is where the power of NPM scripts starts to show itself.

Let’s look at a super basic custom NPM script that outputs “hello world” to the console. Add this to the scripts object of your package.json file:

```
"say-hello": "echo 'Hello World'"
```

he scripts object in your package.json file should look like this:

```
..."scripts": {    "start": "node index.js",    "say-hello": "echo 'Hello World!'"}
```

Try running `npm run-script say-hello` or `npm run say-hello`. The console says, “Hello World!”! We’ve written our first NPM script!

Here’s a quick tip — in order to prevent the default NPM logs from outputting to the console when you execute a script, add the `--silent` flag. This is what your command would look like:

```
npm run --silent say-hello
```

### Calling NPM Scripts Within Other NPM Scripts

One downside of using NPM scripts shows up when your script is fairly complex (and long). This problem is compounded by the fact that the JSON spec does not support comments. There are a few ways around this problem. One way is to divide your script into small single-purpose scripts and then call them within other NPM scripts. The way to call an NPM script within another is straightforward.

```
"scripts": {    
	"say-hello": "echo 'Hello World'",
	"awesome-npm": "npm run say-hello && echo 'echo NPM is awesome!'"
}
```

### Calling Shell and Node Scripts

At times, you may have to write scripts far more complex than ones that can be achieved in 2–3 commands. When this situation arises, one solution is to write bash or JS scripts (or scripts in any scripting language you like) and call them from NPM scripts.

Let’s quickly write a bash script that says hello to you. Create a file called `hello.sh` in your root directory and paste this code in it:

```
#!/usr/bin/env bash
ls
```

Now modify the `package.json` file so that the `scripts` object has this line of code:

```
"bash-hello": "bash hello.sh"
```

Now, when you run `npm run bash-hello`, it asks you for your name and then says hello to you! Brilliant.

You can do the same thing with JS scripts run using node. An advantage of this approach is that this script will be platform independent since it uses node to run. Here’s a slightly more complex JS script to add two integers received as command line arguments (put this in a file named add.js):

```
function add(a, b) {
	return parseInt(a)+parseInt(b);
}
if(!process.argv[2] || !process.argv[3]) {    
	console.log('Insufficient number of arguments! Give two numbers please!');
}
else {
	console.log('The sum of', process.argv[2], 'and', process.argv[3], 'is', add(process.argv[2], process.argv[3]));
}
```

Now add this line to the `scripts` object of the `package.json` file:

```
"js-add": "node add.js"
```

Finally, run the script as an npm script by giving it two numbers as command line arguments:

```
npm run js-add 2 3
```

### Making Our Scripts Cross-Platform

There is one drawback of writing terminal/shell commands in our scripts. This is the fact that shell commands make our scripts platform dependently. This is perhaps what draws our attention to tools like Gulp and Grunt. If your script is written for Unix systems, chances are, it won’t work on Windows, and vice versa.

There are three approaches that you may use:

1. **Use commands that run cross-platform:** Many useful commands are common to Unix and Windows. If your scripts are simple, consider using those.
2. **Use node packages:** You can use node packages like [rimraf](https://www.npmjs.com/package/rimraf) or [cross-env](https://www.npmjs.com/package/cross-env) instead of shell commands. And obviously, you can use these packages in JS files if your script is large and complex.
3. **Use ShellJS:** [ShellJS](https://www.npmjs.com/package/shelljs) is an npm package that runs Unix commands via Node. So this gives you the power to run Unix commands on all platforms, including Windows.

### A Few Use Cases for NPM Scripts

Finally, there is a lot that you can do with NPM scripts. Some use cases are:

- Minification/Uglification of CSS/JavaScript
- Automating the build process
- Linting your code
- Compressing images
- Automatically injecting changes with BrowserSync

And a lot more. To learn about how to automate the above-mentioned tasks using NPM scripts, check out [this brilliant article](https://css-tricks.com/why-npm-scripts/) on the topic.

More use cases: [awesome-npm-scripts](https://github.com/RyanZim/awesome-npm-scripts)

## References

[1] [Introduction to NPM Scripts - FreeCodeCamp](https://www.freecodecamp.org/news/introduction-to-npm-scripts-1dbb2ae01633/)

