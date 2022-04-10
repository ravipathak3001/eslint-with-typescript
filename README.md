# eslint-with-typescript
ESLint is a JavaScript linter that you can use to lint either TypeScript or JavaScript code.


## Installation and setup
Run the following commands to setup ESLint in your TypeScript project.

<code>npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin</code>
Create an .eslintrc file.

<code>touch .eslintrc</code>
In it, use the following starter config.
```yaml
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "plugins": [
    "@typescript-eslint"
  ],
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/eslint-recommended",
    "plugin:@typescript-eslint/recommended"
  ]
}
```

## Ignoring files we don't want to lint
Create an .eslintignore in order to prevent ESLint from linting stuff we don't want it to.

<code>touch .eslintignore</code>
Then add the things we want to ignore. In the following code sample, we're ignoring the dist/ folder that contains the compiled TypeScript code. If you're compiling your TypeScript code to a different folder, make sure to use that instead of dist. You should be able to find this in your .tsconfig (see the previous guide).

<code>node_modules
dist</code>

Adding a lint script
In your project package.json, lets add a lint script in order to lint all TypeScript code.

```yaml
{
  "scripts": {
    ...
    "lint": "eslint . --ext .ts",
  }
}
```

Ready to try it out? Let's run the following command.

<code>npm run lint</code>

For me, since my src folder only has a single index.ts in it that prints out some text with console.log(), I don't see anything after I run the command.

<code>src/index.ts</code>

```yaml
console.log('Hello world!')
```

What if we wanted to disallow console.log statements in our code?

## Rules
There are three modes for a rule in eslint: <strong>off</strong>, <strong>warn</strong>, and <strong>error</strong>.

<ul>
  <li>"off" means 0 (turns the rule off completely)</li>
  <li>"warn" means 1 (turns the rule on but won't make the linter fail)</li>
  <li>"error" means 2 (turns the rule on and will make the linter fail)</li>
  </ul>
  
### Adding a rule
In .eslintrc, add a new attribute to the json object called "rules".

```yaml
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "plugins": [
    "@typescript-eslint"
  ],
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/eslint-recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": { }
}
```
Rules get added as keys of this rules attribute, and you can normally find the eslint base rules here on their website via the Rules docs.

We want to add the no-console rule, so here is an example of how we can make the linter fail (throw a mean error code) if it encounters a console.log statement with the no-console rule set to error.

We update the .eslintrc

```yaml
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "plugins": [
    "@typescript-eslint"
  ],
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/eslint-recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": { 
    "no-console": 2 // Remember, this means error!
  }
}
```

And then run the linter again.

<code>npm run lint</code>

And we should get an angry linting result.

<code>
/simple-typescript-starter/src/index.ts

  2:1  error  Unexpected console statement  no-console
    ✖ 1 problem (1 error, 0 warnings)
  </code>
  
And if we wanted, we could turn the rule off by setting it to 0 - off.

```yaml
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "plugins": [
    "@typescript-eslint"
  ],
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/eslint-recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": { 
    "no-console": 0 
  }
}
```

Run the linter.

npm run lint
...and silence.

## Using rules in a real-life project
There's a reason that all three of these modes exist. When you set the linter to off for a rule, it's because you and your team probably don't care about that rule in a particular base configuration you're using (we're currently using the ESLint recommended config but you can also go and use <a href="https://www.npmjs.com/package/eslint-config-fbjs">Facebook's</a> or several other companies' configs as starting points instead).

When you set the rule to **error - 2**, it means that you don't want the code that breaks your coding conventions to even make it into the repo at all. I think this is a great act of professionalism and empathy towards the codebase, your fellow teammates, and future maintainers. A popular approach to actually enforce code conventions with this tool is to set up your project with a tool like <a href="https://github.com/typicode/husky">Husky</a> so that when a teammate tries to commit code or push code, you can tell your linter to check the code first before the operation executes. It's a great habit, though sometimes, if the rules are overly restrictive, it can slow down and annoy your teammates.

To remedy overly restrictive rules, the **warn - 1** setting means that yes, you want you and your team to adhere to that rule, but you don't want it to prevent you from moving forward.

### Adding a plugin (features)
ESLint also allows you to add **one-off** features to your config. These are known as plugins.

Here's a fun one. It's called <a href="https://github.com/buildo/eslint-plugin-no-loops">no-loops</a>.

Check out this list of other <a href="https://github.com/dustinspecker/awesome-eslint">awesome-eslint</a> plugins and configs.

**no-loops** is a plugin that will enable you to enforce a convention specifying that for and while loops are illegal and that you should use functions like map and forEach instead.

Install it like this.

<code>npm install --save-dev eslint-plugin-no-loops</code>
And then update your .eslintrc with no-loops in the "plugins" array, and add the rule to the "rules" attribute like so.

```yaml
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "plugins": [
    "@typescript-eslint",
    "no-loops"
  ],
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/eslint-recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": {
    "no-console": 1,
    "no-loops/no-loops": 2
  }
}
```

Now if we update our code to include a for loop...

```yaml
src/index.ts
console.log('Hello world!');

for (let i = 0; i < 10; i++) {
  console.log(i)
}
```

And run the lint command again...

<code>npm run lint</code>

We'll see the errors that restricts loops appear.

<code>
/simple-typescript-starter/src/index.ts
  2:1   warning  Unexpected console statement                   no-console
  2:1   error    'console' is not defined                       no-undef
  4:1   error    loops are not allowed                          no-loops/no-loops
  5:3   warning  Unexpected console statement                   no-console
  5:3   error    'console' is not defined                       no-undef
  5:17  error    Missing semicolon                              babel/semi
  5:17  error    Missing semicolon                              semi
  6:2   error    Newline required at end of file but not found  eol-last

✖ 8 problems (6 errors, 2 warnings)
  3 errors and 0 warnings potentially fixable with the `--fix` option.
  </code>
  
### Extending a different base config
Let's say that you wanted to start with a different base config- Shopify's, for example.

Here's how to do that.

Looking at the readme, we need to install it by running:

<code>npm install eslint-plugin-shopify --save-dev</code>

Update our .eslintrc

```yaml
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "plugins": [
    "@typescript-eslint"
  ],
  "extends": [
    "plugin:shopify/esnext"
  ],
  "rules": {
    "no-console": 1
  }
}
```

You can add several base configs to your project by including them in the array, though you may end up seeeing the same linting rules twice or more.

Now when we run the lint command again with **npm run lint**, we can see a few errors reported based on the base Shopify config and our **no-console** rule.

<code>
/simple-typescript-starter/src/index.ts
  2:1   warning  Unexpected console statement  no-console
  2:1   error    'console' is not defined      no-undef
  2:28  error    Missing semicolon             babel/semi
  2:28  error    Missing semicolon             semi

✖ 4 problems (3 errors, 1 warning)
  2 errors and 0 warnings potentially fixable with the `--fix` option.
Fixing linted code with ESLint
You might have noticed that at the end of the error message, it says "2 errors and 0 warnings potentially fixable with the --fix option."
  </code>

You can run ESLint and tell it to fix things that it's able to fix at the same time.

Using the **--fix** option, let's add a new script to our **package.json** called **lint-and-fix**.

```yaml
{
  "scripts": {
    ...
    "lint": "eslint . --ext .ts",
    "lint-and-fix": "eslint . --ext .ts --fix"
  },
}
```

Running this command against our code, I expect that it will put a semicolon on the console.log statement that we had.

Let's run it.

<code>npm run lint-and-fix</code>

The result is that less errors are reported. We don't see the error about semi-colons anymore.
<code>
/simple-typescript-starter/src/index.ts
  2:1  warning  Unexpected console statement  no-console
  2:1  error    'console' is not defined      no-undef
  </code>
  
Because sure enough, the linter added the semi-colon.

<code>
src/index.ts
console.log('Hello world!');
  </code>
