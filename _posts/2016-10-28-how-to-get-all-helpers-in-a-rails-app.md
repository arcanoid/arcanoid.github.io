---
layout: post
comments: true
title: How to get all helpers in a Rails application
description: How to get a list of all customs helpers and methods in a Ruby on a Rails application  
---

I recently started a new project that would enable its users to produce as many unit ([Rspec]("https://www.relishapp.com/rspec/rspec-rails/v/3-5/docs/helper-specs/helper-spec")) tests as possible through some automated commands (a ruby gem called [SpecProducer]("https://github.com/arcanoid/spec_producer")) for every kind of known resources (views, controllers, routes etc). What I actually wanted to achieve was to produce a list of pending tests for each helper added by the project's developer (aka not built-in helpers, only custom ones). I came to see that it was quite difficult (at least to me) to get a list of all helpers and for each helper the list of its methods, so I thought it might be useful to someone and added it in this small tip-post.

So, let's get started!

What I first wanted to achieve was to list all helpers added by the developer of the project. All helpers added are positioned inside the app/helpers namespace of the RoR project. To do so, one can use ActionController::Base to retrieve all that info as a list of names (be warned each name doesn't include "helper" in it. Eg. LinksHelper will be returned as "links") of all the helpers inside that namespace.

{% highlight ruby %}
helper_names = ActionController::Base.all_helpers_from_path 'app/helpers'
{% endhighlight %}

The outcome of this would be the following:

{% highlight ruby %}
[ "links", "sample/list" ]
{% endhighlight %}

Though, the above list is pretty much useless if you don't actually get a list of helper modules. To do so one can either use constantize or continue using ActionController::Base capabilities as follows:

{% highlight ruby %}
helpers = ActionController::Base.modules_for_helpers helper_names
{% endhighlight %}

The outcome of this would be a list of fully functional Helper Modules:

{% highlight ruby %}
[ LinksHelper, Sample::ListHelper ]
{% endhighlight %}

From there on you can use this list as you see fit. An extension I wanted for each item of that list, was the methods defined in each of those helpers. So by calling the instance_methods of each one I was able to collect all method names for each helper.

{% highlight ruby %}
helpers.first.instance_methods
{% endhighlight %}

Finally, that list includes all the methods (as symbols) of the referring helper. 

The above allowed me to dynamically create helper specs with proper naming and with proper 'pending method' tests. Hopefully it was useful to you too.
