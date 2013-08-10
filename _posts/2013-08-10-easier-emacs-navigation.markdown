---
layout: post
title: "Easier Emacs navigation"
date: 2013-08-10 18:42
comments: true
categories: emacs
---

### Here's a quick Emacs movement tip

I realized some time ago that I get annoyed with default up and down
movement with C-p and C-n. The problem with those two commands is that
I am rarely interested in moving by just one line. Because of that I was mostly
depending on C-s for movement. What I did was: move down a few
lines, recognize a place I want to jump to and C-s to near vicinity.

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

What that did is it allowed me to move by 5 lines up and down by
pressing Shift along the default C-n and C-p combinations. So, for
example C-S-n moved me down 5 lines. This turned out to be a great
idea but they keys were still a bit awkward. That is why yesterday I
changed that code to be something like this:

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
