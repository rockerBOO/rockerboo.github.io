---
layout: post
title:  "Brunch for Back-end Developers"
date:   2015-10-12 15:16:48
categories: brunch back-end
feature-img: "img/brunch.png"
---
[Brunch][brunch] is a great tool for building your assets in quick and efficient ways. It is very easy to configure with a lot of intergration possibilities.  My introduction to working with Brunch was with the [Phoenix Framework][phoenix], a new web framework built on [Elixir][elixir].

## Skeletons

[Skeletons](http://brunch.io/skeletons.html) are great ways to jump right into making applications using the brunch system. They require a blank project to roll them out and some of them are hugely outdated.

Once you find the stack you will be working with it is setup to download it from GitHub.

{% highlight bash %}
brunch new <skeleton-URL> [optional-output-dir]
brunch new gh:brunch/dead-simple
{% endhighlight %}

## Configuring Packages

Brunch uses node and NPM to manage its packages. All of the dependancies can be listed in package.json. Ideally you want to pick a feature version and just get the latest updates bug and security fixes.

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

After this you can run the install on NPM, which will download and install all the dependancies for you. This is where you add additional brunch packages that you find you want or remove the ones you don't want.

{% highlight bash %}
npm install
{% endhighlight %}

## Configuring Brunch

Most of the brunch packages in the "dead-simple" skeleton will help to minify and cleanup css and javascript and adds coffee script support.

The [configuration documentation](
https://github.com/brunch/brunch/blob/master/docs/config.md) goes over all the options you can modify but can be a little hard to grasp what does what.

## `files`

This part of the configuration allows you to concatenate all your javascript and css into their own single file, which is great for creating modules and having it all put together for you.

By Default brunch uses Require.js functionality for JavaScript. This allows you to keep modules in their scope and use them like you would in Node.js.

{% highlight html %}
<script type="text/javascript" src="/app.js"></script>
<script type="text/javascript">
	var Package = require('js/package');
</script>
{% endhighlight %}

Css uses basic concatenation so its pretty much what you'd expect there. If you want SASS or LESS or STYL support there are brunch packages for each, using the PostCSS-brunch plugin.

## `paths`

The idea is that you will create these files in a separate place than your normal publicly accessible files and use brunch to build them into the files you'll use. You can customize where it will output the resulting files in the `paths` option. In watched part of paths will be where you put your raw files at before it builds them into the public folder. It will recursively find all the files in these folders, so organize them as you would like. `_file.css` will prevent the file from being built, which is like partials in SASS.

{% highlight coffeescript %}
paths:
  public: 'public'
  watched: ['web']
{% endhighlight %}

## Running Brunch

You can have brunch watch your files and compile them when any changes happen. It also has a built in web server if you don't have any backend setup to serve the files.

{% highlight bash %}
brunch --watch [--server]
{% endhighlight %}

## Deploying Brunch

Deploying brunch is quite simple as you won't need to be watching for file updates anymore. You can setup specific overrides in the configuration for what changes you want to have happen on production vs development like minified files and removing comments and source maps.

{% highlight bash %}
brunch build --production
{% endhighlight %}

Now you should be good to go serving up those assets that have been properly minified, concatenated, and any other build operations you wish to have.

Brunch is a great tool that runs fast and is simple to configure to do a lot of great things. It will allow you to increase your development speed through easy to see use compiling on save, as well as knowing it will come out great when you push it to production.

[brunch]: http://brunch.io
[phoenix]: http://www.phoenixframework.org/
[elixir]: http://elixir-lang.org/