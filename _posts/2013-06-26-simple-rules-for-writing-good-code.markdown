---
layout: post
title: "Good enough"
date: 2013-06-26 19:49
comments: true
categories: code
---

### Code quality

There are many approaches to code quality subject: many books and blog
post have been written about it.

I don't want to focus on any of the methodologies in this post - what
I want to do instead is to highlight the fact that although most of the
rules that have been accepted in recent years are correct and lead to
better code, ther are not ment to be followed in every situation.
In fact I'd judge that following them in 80 to 90% cases is more then enough.

If the above statement is true it means that there's up to 1 in 5
situations in your everyday work, where you shouldn't use Single
Responsibility Principle, you shouldn't care that much about Law of
Demeter violation, etc.

I know that might sound strange: "If the rules are correct and they lead
to better code - why shouldn't we use them 100% of the time?"

My answer to that is composed of two parts:

- getting to 90% is quite easy - getting the last 10% right - can take
  a lot more time

How much more? It's sometimes hard to say.

Is it worth it? In most cases NO.
  
In reality your client is not paying you to spend 3-4 more hours on
refactoring your code, when it already: meets his needs, is bug free,
and is 80-90% clean (so it will be fairly easy to change and maintain
in the future)
  
- Some of the rules when applied to smaller code bases/ simpler
  problems are just not needed and should be applied "Just-In-Time" as
  the scope/complexity grows

What that all means is that when you think about code quality - you
should focus about tradeoffs.

### Given a sufficient (unlimited) amount of time, how far would you go with your code?

I used to be a perfectionist when it comes to those matters. I always
tried to make the code the best and the cleanest it can be. But then,
with experience, I realized just how arbitrary and context dependent
metrics and heuristics really are. Some of them are less, some of them
more, but in my opinion - only a few are always true.

After I came to this conclusion I started to look for ways of writing
code that is __"good enough"__. My standard for __"good enough"__ is
not easy (for me) to get to - after I write the code, and I write the
tests for the parts I think need testing, I spend a fair amount of
time refactoring and making it simpler. But I try to delay using
'techniques' and 'laws' as long as I can.

### Comparing Apples and Oranges

I will not spend much time here giving examples of what I'm talking
about - beacause I want to convey a more general thought and not a
solution to particular problem - but still I will give you one simple
case, where I think delaying best practices is a good thing.

Lets say, for example, I have 2 objects **Apples** and **Oranges**,
and I have a search functionality, that searches through all text
fields of both **Apples** and **Oranges** and presents results as a
list. My first thought a year or two ago would be to write a presenter
for both **Apple** and **Orange** object types. Then I would wrap
resulting objects in appropriate presenters and then in the __view__
use a method, called for example `#result_title`, defined in both
Presenters which returns a proper title text.

{%highlight ruby %}

class Apple < Struct.new(:kind, :weight)
end

class Orange < Struct.new(:producer, :weight)
end

# presenters
class ApplePresenter
  # [...]
  def result_title
    "An apple of #{kind} it's weight is #{weight}"
  end
end

class OrangePresenter
  # [...]
  def result_title
    "Orange produced by #{producer} it's weight is #{weight}"
  end
end



# Wrapping results in presenters..
results.map &:wrapped_in_presenter

# .. and then to show them in the view:
<li>result.result_title</li>

{% endhighlight %}

That is a good approach, and if you have more then two objects or more
then one place where you present them, you should most definitely
think about using it. It is also doing it "by the book", but doing it
by the book is not always what you have to do! In a case like that
when I have only two types of objects (and adding new type in the next
few iterations does not seem that probable) using a `#result_title`
method straight in **Apple** and **Orange** would be actually a lot
simpler, and wouldn't make the code bad.

So using the simpler solution would be the way I would go these
days. I would even try, at first, to not to add the method in the
first place, if the logic I had to put in the view wouldn't be very
complicated. What I mean by that is that, assuming **Apple** had
`title` field and **Orange** `name` field, I would not worry about
using `result.title || result.name` if I was sure a call to `#title`
and `#name` returned a __falsy__ value in case the receiver didn't
have that field.

### Conclusion

I realize that what I have written above can be controversial. I would
find it controversial 2 years ago. But, at some point, you have to
take into account that code quality __IS NOT__ the most valuable thing
in your work. Your code should be __GOOD ENOUGH__ to perform the task
at hand. And yes, it should be extensible, well tested, and avoid
repetition but what is more important it should meet __"client's"__
needs and it should __always work without errors!__
