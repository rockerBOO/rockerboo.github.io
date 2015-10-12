---
layout: post
title:  "Brunch for Back-end Developers"
date:   2015-10-12 15:16:48
categories: brunch back-end
feature-img: "img/brunch.png"
---
[Brunch][brunch] is a great tool for building your assets in quick and efficient ways. It is very easy to configure with a lot of integration possibilities.  My introduction to working with Brunch was with the [Phoenix Framework][phoenix], a new web framework built on [Elixir][elixir].

## Skeletons

[Skeletons](http://brunch.io/skeletons.html) are a great way to jump right into making applications using the Brunch system. They require a blank project to roll them out. Downside is some of them are hugely outdated.

Once you find the stack you will be working with its setup to download from GitHub. For this example I will be using the `dead-simple` skeleton. It has simple CSS/Javascript building processes.

{% highlight bash %}
brunch new <skeleton-URL> [optional-output-dir]
brunch new gh:brunch/dead-simple
{% endhighlight %}

## Configuring Packages

Brunch uses [Node.js][node] and [NPM][npm] to manage its packages. All of the dependencies can be listed in `package.json`. Ideally you want to pick a feature version and just get the latest updates bug/security fixes.

`package.json`

{% highlight json %}
"dependencies": {
    "javascript-brunch": ">= 1.0 < 1.9",
    "coffee-script-brunch": "> 1.0 < 1.5",
    "css-brunch": ">= 1.0 < 1.9",
    "uglify-js-brunch": ">= 1.0 < 1.9",
    "clean-css-brunch": ">= 1.0 < 1.9",
	...
{% endhighlight %}

After this you can run the install on NPM, which will download and install all the dependencies for you. This is where you add additional Brunch packages that you find you want or remove the ones you don't want. The pattern for package names is `*-brunch`.

To install any additional packages, use the NPM install system.

{% highlight bash %}
npm install
{% endhighlight %}

## Configuring Brunch

Most of the Brunch packages in the "dead-simple" skeleton will help to minify and cleanup CSS and Javascript and adds Coffeescript support.

The Brunch [configuration documentation](
https://github.com/brunch/brunch/blob/master/docs/config.md) goes over all the options you can modify. It is a little hard to grasp the scopes and power of certain features. It will also usually continue to work, even if you have something configured improperly.

## `files`

{% highlight coffeescript %}
files:
  javascripts:
    joinTo:
      'js/app.js': /^(web\/js)/
  stylesheets:
    joinTo:
      'css/app.css': /^(web\/css)/
{% endhighlight %}

This part of the configuration allows you to concatenate all your Javascript and CSS into their own single file, which is great for creating modules and having it all put together for you.

	/web/js/package.js
	/web/js/package2.js

	> /public/js/app.js

	/web/css/sample.css
	/web/css/sample2.css

	> /public/css/app.css

By default Brunch uses [Require.js][requirejs] modular functionality for Javascript. This allows you to keep modules in their scope and use them like you would in Node.js.

{% highlight html %}
<script type="text/javascript" src="/app.js"></script>
<script type="text/javascript">
	var Package = require('js/package');
</script>
{% endhighlight %}

CSS uses basic concatenation so its pretty much what you'd expect there. If you want SASS or LESS or STYL support there are Brunch packages for each, using the [PostCSS-brunch][postcss] plugin.

## `paths`

The idea is that you will create these files in a separate place than your normal publicly accessible files. Use Brunch to build them into the files you'll use. You can customize where it will output the resulting files in the `paths` option. In watched part of paths will be where you put your raw files at before it builds them into the public folder. It will recursively find all the files in these folders, so organize them as you would like. `_file.css` will prevent the file from being built. This is like partials in SASS.



{% highlight coffeescript %}
paths:
  public: 'public'
  watched: ['web']
{% endhighlight %}

## Running Brunch

You can have Brunch watch your files and compile them when any changes happen. It also has a built-in web server if you don't have any back-end set up to serve the files.

{% highlight bash %}
brunch --watch [--server]
{% endhighlight %}

## Deploying Brunch

Deploying Brunch is quite simple as you won't need to be watching for file updates anymore. You can setup specific overrides in the configuration for what changes you want to have happen on production/development/test environments like minified files and removing comments/source maps.

{% highlight bash %}
brunch build --production
{% endhighlight %}

Now you should be good to go serving up those assets that have been properly minified, concatenated and any other build operations you wish to have.

Brunch is a great tool that runs fast and is simple to configure to do a lot of great things. It will allow you to increase your development speed through easy to see use compiling on save, as well as knowing it will come out great when you push it to production.

[requirejs]: http://www.requirejs.org/
[brunch]: http://brunch.io/
[npm]: https://www.npmjs.com/
[node]: https://nodejs.org/
[phoenix]: http://www.phoenixframework.org/
[elixir]: http://elixir-lang.org/
[postcss]: https://github.com/iamvdo/postcss-brunch/