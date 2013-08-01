# What is This Monstrosity? #

Jekyll, despite being a static site generator, is very HTML5 History- and AJAX-friendly. The key lies in [History.JS](https://github.com/balupton/history.js), built by Benjamin Arthur Lupton, and a small piece of Javascript.

## What this does ##

By utilizing History.JS, we can make any Jekyll-built site use AJAX and a bit of jQuery magic to load content *without* refreshing the page, and *with* all the cleanliness of the HTML5 pushState functionality.

## How it works ##

Jekyll, as you are probably aware, is a staic site generator. This seems to be the antithesis of the complex, AJAX-driven websites that we use on a day-to-day basis. There's **no database**, **no server-side scripting**, and **it works everywhere**. I find a great deal of beauty in that simplicity, but I didn't want to lose any functionality that I could get with something driven by PHP or Ruby on the server.

At its core, this HTML5 History-friendly version of Jekyll catches any internal links and fetches the full page. From there, it searches for the post's content (via a certain selector) and injects that into the DOM. History.JS takes care of changing the URL in the browser's address bar, and allowing for full forward-and-back button functionality. This ensures that deep-linking works exactly like it should, too.

*That's sweet.*

The tech that makes this possible is acutally incredibly simple. All one needs to do is use History.JS and include this piece of code into their primary Javascript file.

	jQuery(document).ready(function($) {

		var siteUrl = 'http://'+(document.location.hostname||document.location.host);

		$(document).delegate('a[href^="/"],a[href^="'+siteUrl+'"]', "click", function(e) {
			e.preventDefault();
			History.pushState({}, "", this.pathname);
		});

		History.Adapter.bind(window, 'statechange', function(){
			var State = History.getState();
			$.get(State.url, function(data){
				document.title = $(data).find("title").text();
				$('.content').html($(data).find('.content'));
				_gaq.push(['_trackPageview', State.url]);
			});
		});

	});

Really, though, it's a little more complicated than that. The best way to get functionality out-of-the-box is to clone this repo out of Github and use it as a starting point for your own creation. You can change the formatting, the types of layouts &mdash; whatever you want. You can strip away my [*wonderful* grid](https://github.com/joelhans/Colonnade), and you can free yourself from my CSS. **The only caveat is that you must maintain a similar structure in your layout.html file. Or know what you're doing.**

	<section class="content">
		{% raw %}{{ content }}{% endraw %}
	</section>

If you get rid of that "content" class, the AJAX call in the above snippet will fail to load the requested page, and that's no good at all. Of course, you can change that class *and* change the class name the Javascript function looks for.

{% highlight javascript %}
$('.content').html($(data).find('.YOUR CLASS NAME'));
{% endhighlight %}

There. That wasn't that hard, was it?

### Titles ###

As a little aside, I'll take a minute to talk titles. The above code is capable of changing the document's title on-the-fly during an AJAX call, thanks to this little bit of code:

	document.title = $(data).find("title").text();

This works in essentially the same way as the Javascript that pulls the page's content, except it grabs the title of the page we call with AJAX and inserts that into the current document. It assumes that you have your titles set up properly, which this default Jekyll site does. *Just another reason to use this as a solid template for your next Jekyll-based project*.

### Google Analytics ###

Be not afraid! The piece of JavaScript that powers the AJAX-iness of this Jekyll template is also capable of handling the changes via a `_gaq.push` function. This assumes that you have the proper Google Analytics code (with your own account #) set up in the header of the `layout.html` page, which can be found in `_layouts/`. The code is simple:

	_gaq.push(['_trackPageview', State.url]);

## Why ##

Oftentimes, HTML5 History and AJAX seem like "overkill" for the kinds of sites that Jekyll is usually responsible for. Let's be honest &mdash; we're not making the next Gmail with Jekyll. We're not even making a web app. That doesn't mean there aren't good use cases for something like this. I built it for a website for a musician &dash; I didn't want visitors to have to choose between playing a sample track and being able to browse the site without interrupting their listening. This seems to work pretty darn well.

**Thanks for looking!** I want to see what you can come up with. Get in touch at [hi@designbyjoel.com](mailto:hi@designbyjoel.com). Remember: *This is actually really easy.*