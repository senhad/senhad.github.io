---
layout: post
title:  "Rails Magic"
date:   2017-05-07 22:41:39 +0000
---


Coming from Sinatra, one thing that constantly amazes me is the level of abstraction present when coding in Rails. 
It makes writing code very efficient, but I feel that at some point it gets too "magical" as well. It is very important to understand the workings behind "magic" to use it successfully. 
Let me explain what I mean.

Let's say, we want to create a list of blog posts: 

```
<ul>
<% @posts.each do |post| %>  
      <li><%= post.title %></li>
<% end %>
</ul>
```

In Sinatra, if we wanted to add the hyperlink to each post listing it would need to be hardcoded in HTML: 

```
<ul>
<% @posts.each do |post| %>  
     <li><a href='posts/'<%= post.id %>'<%= post.title %></a></li>
<% end %>
</ul>
```

Smart people have noticed this and realised that most of the process is repetitive and can be automated (remember DRY?) and therefore decided to take advantage of abstraction and conventions to make life easier for us in Rails. 

Enter ActionView. 

It is a set of templates written using embedded Ruby in tags mingled with HTML that helps to remove common boilerplate code and provides common behavior for forms, dates, and strings. It is basically a framework within Rails that makes writing HTML easier and better. 

Let's look at example of `link_to`:

`<%= link_to ('About Page', "about_path" %>` is similar to `<a href="/about">About Page</a>`. 

`Link_to` requires two arguments: first one is equivalent to inner text in HTML code, and the second one to the route name. 

To understand the purpose behind this we need to head back to routes.rb where we have something like this:

`get '/about' => 'static#about'` 

If for some reason we wanted to change `/about` route to `/about_us` we would need to manually change every `href` link that includes `/about`. Instead Rails "names" routes, so `/about ` becomes simply `about_path`. `/about ` can be changed later but the name of the route will remain same. In essence `about_path` is really just a method, that when called provides actual route. 

Even better, names of routes are defined automatically by Rails so `/about ` will be automatically `about_path` unless we specifically change it.

So let's go back to the problem from the top. In Rails it would look like this: 

```
<ul>
<% @posts.each do |post| %>  
     <li><%= link_to (post.title, post_path(post.id)) %></li>
<% end %>
</ul>
```

As you can see,  `<a href='posts/<%= post.id %>'<%= post.title %></a>`  and `<%= link_to (post.title, post_path(post.id)) %>` are same. 

`post_path` refers to `'posts/:id'`. Another cool thing about it, is that is able to receive objects for dynamic pages. So in this example, we have `post_path(post.id)` which will transfer to `'posts/:post.id'`.

But Rails is even smarter than that. We can just write this:

```
<ul>
<% @posts.each do |post| %>  
     <li><%= link_to (post.title, post_path(post)) %></li>
<% end %>
</ul>
```

And Rails will automatically extract id from `post ` object to generate the route.

Rails can go even higher in terms of abstraction and into realm of "magic". We can implicitly call `post_path ` like this:

```
<ul>
<% @posts.each do |post| %>  
     <li><%= link_to (post.title, post) %></li>
<% end %>
</ul>
```

At this level, readability of code might be an issue as it looks too "magical". 

Programmers choose different levels of abstraction based on what they are comfortable with, but as long as they understand the process behind, they should be fine. 



