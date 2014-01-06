---
layout: post
title: "Easier Emacs navigation"
date: 2013-08-10 18:42
comments: true
categories: emacs
---

**EDIT 2** I moved to using [smartparens](https://github.com/Fuco1/smartparens/wiki/Working-with-expressions) for navigation - in most cases it does exactly
what I need.

**EDIT** Since trying this experiment I reverted to old behaviour fot C-n and C-p.
Moving by 5 lines was just to much in most cases, but I still keep it around
as C-S-n and C-S-p and I use it all the time.

### Here's a quick Emacs movement tip

I realized some time ago, that I get annoyed with default up and down
movement with C-p and C-n. The problem with those two commands is that
I am rarely interested in moving by just one line. Because of that I was mostly
depending on incremental search (C-s) for movement. When I wanted to move down a few lines,
I scaned area near the goal for a suitable search fraze, and then I jumped there using C-s.

As I was not 100% happy with that workflow so I decided to try something
new, and this is what I added to my config files:

{% highlight cl %}
(global-set-key (kbd "C-S-n")
                (lambda ()
                  (interactive)
                  (ignore-errors (next-line 5))))

(global-set-key (kbd "C-S-p")
                (lambda ()
                  (interactive)
                  (ignore-errors (previous-line 5))))



{% endhighlight %}

This allowed me to move by 5 lines up and down by pressing Shift along the default
C-n and C-p combinations. For example C-S-n moved me down 5 lines. This turned out to be a great
idea but they keys were still a bit awkward. That is why yesterday I changed that code
to be something like this:

{% highlight cl %}
(global-set-key (kbd "C-n")
                (lambda ()
                  (interactive)
                  (ignore-errors (next-line 5))))

(global-set-key (kbd "C-p")
                (lambda ()
                  (interactive)
                  (ignore-errors (previous-line 5))))

(global-set-key (kbd "C-S-n") 'next-line)
(global-set-key (kbd "C-S-p") 'previous-line)

{% endhighlight %}

Now by default C-n moves me 5 lines down and C-p 5 lines up, and if I
want to move by just one line I use C-S-n and C-S-p, so far this is
really working quite well for me!
