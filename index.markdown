---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---
# README.MD
My name is Liam Loughead, and I enjoy writing code for anything that can be programmed. My interests span from robotics to game design to networking utilities. Here you'll find posts that give some insight into the various projects that are on my [github](https://github.com/Snapwhiz914). I'll explain my motivation, explanations of my thought process, and outcomes - anything you wouldn't find in a repository's README.

Since I've only recently (as of 2024) started this blog, I have numerous projects that lie undocumented on GitHub and on my PC.
 - Projects that were committed to GitHub before I started this blog will have their title prefixed with "Initial Commit: "
 - Projects that were never committed to GitHub but deserve a post nonetheless will have their title prefixed with "Uncommitted: "

Dates on these posts will not correspond with the actual time of their making. If the date is relevant then I will mention it in the post.

The posts list below is organized starting from most recent.

# Posts
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
    <br>
  {% endfor %}
</ul>
