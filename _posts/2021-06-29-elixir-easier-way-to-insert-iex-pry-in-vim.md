---
layout: post
title: Lazy way to insert require IEx;IEx.pry() in Vim 
tags: [elixir, vim, iex, pry]
comments: true
---

When I'm writing code in Elixir, I've been using `require IEx; IEx.pry()` a lot to debug my code that it became annoying having to type it all the time again and again. Hence I was thinking about a lazier way to have it inserted there in Vim and of course I was not the only one annoyed with this problem.

```vim
map ,P Orequire IEx; IEx.pry()<ESC>
map ,p orequire IEx; IEx.pry()<ESC>
```

After inserting above code into your `.vimrc` you can basically insert pry line into your code with the keyboard shortcuts `,p` and `,P`. Uppercase version of the shortcut inserts the line above the current line and lowercase version inserts the line below the current line. 

