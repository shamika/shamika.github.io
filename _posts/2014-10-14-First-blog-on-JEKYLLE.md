---
layout: post
title: My First blog post on github pages
---

This is a my first post on github pages using jekyll. Still in the evaluating process will soon pubslishing some posts. Follwoing is my test on some highlighted ruby code.

{% highlight ruby %}
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}

