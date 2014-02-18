---
layout: post
title: "Boundaries"
date: 2014-02-16 10:11
comments: true
categories: code
---


What is the number one cause of errors in code that you write day to day?

Your answer depends on the kind of software that you create, so
let's narrow it a bit: What is the most error prone area when
writing modern day software using some kind of database for storage and
http for transportation layer, and web or mobile interface?

This time the answer I'll get from you, can still be a million different things,
depending on the context and your previous experiences.

My answer? It's simple - boundaries.

### Boundaries

When I say boundaries I mean connections between different layers
of services of your application. Two most obvious boundaries are:

- between database and back end system
- between backend system and frontend

But there are actually a couple of more subtle boundaries we have to deal with
every day:

- email sending service
- background processing service
- indexing/searching service
- caching layer

Some boundaries that depend on the actual implementation:

- framework's code for rendering views and JavaScript code (if
we're using two different languages for those things)

- framework's code for keeping database schema up to date and code for accessing
this data (loading seed data)

- code for presenting the same resource in different formats

..and others.

### Why are boundaries error prone?

When you communicate between boundaries you have to transport data from one side
to the other - and that is the problem, because this 'data' can, and often does - mean
different things on both sides.

For example on one side we have an entity read and instantiated from the database, let's
say it's a type of Active Record model. On the other side you want to get it's JSON
representation and you only want to see certain kind of attributes.

This creates coupling between the structure of our active record entity and the structure
of it's representation. Every time any of those things changes you have to at least
look at the other and in some cases you have to update it.

"OK" you will say "but why is this a problem?" It's not a problem - it's the way things
are - but in my opinion it's the source of most errors we have to deal with day-to-day.

There are a couple of strategies that help us deal with boundaries and their complexity.
Which tactic to choose depends on the boundary itself. For example in the case I mention
above, the best way to keep safe - no matter what language or framework you choose is
to write solid suite of unit tests that check EVERY argument is present in the produced
JSON representation.

But there are other possibilities. For example let's say we have a model: "Task", this
task can be scheduled and assigned to multiple owners. Now lets further assume that we
want to email every owner if any of the details of the Task change.

Because there can be a lot of owners for every task we want to offload sending emails
to a background job. This job would take Task id as a parameter and it would
send emails to all owners. Here is our boundary.

On the app side - it's easy to get Task id and pass it to a background queue. In the
background worker all depends on the tool and the language.

For example, let's say background worker is written in Ruby, it might look like this:

{% highlight ruby %}

class NotifyTaskChanged
  def process(task_id, notifier = TaskNotifier)
    task = Task.with(id: task_id)
    task.owners.map { |o| notifier.new(o, task).deliver }
  end
end

{% endhighlight %}

And if it was written in Java it could be written as follows:

{% highlight java %}

public class NotifyTaskChanged {
  public void process(String taskId, INotifier taskNotifierType) {
    INotifier n;
    Task task = TasksStore.findWithId(taskId);

    for (Owner o : task.getOwners()) {
      n = new taskNotifierType(o, task);
      n.deliver();
    }
  }
}

{% endhighlight %}

I purposefully omitted error checking for both cases.

Now lets say we call this worker in a wrong way, for example we forget to pass the
id (we pass nil instead). When will we now something is wrong?

- in ruby: when the task is executed in background queue
- in java: when the code is compiled or at latest when the code is being used to offload the
task to background queue

So we can see that statically typed version would let us know something is up faster. You
could write extensive unit tests for both versions but it would never change this distinction.

Now if you have any experience at writing robust code, you would protect ruby's version
by writing it more or less like that:


{% highlight ruby %}
# while offloading
if task.present?
  NotifyTaskChanged.new.process(task.id)
  # ...

# in notifier
def process(task_id, notifier = TaskNotifier)
  raise "Wrong task_id" if task_id.missing? || task_id.not_valid_id?
# ...

{% endhighlight %}

But with that approach:

1. you have to always remember to use those safeguards
2. with them your ruby code starts to get as (un)readable as java


We could easily construct an example where (dynamic language) Ruby
would have advantage, like if we wanted to build some objects based
on the input we got from an API call, etc. But the point here is
not to compare languages - it's more about noticing that you should
take care when writing code on the boundaries, and that you should
use your tools to their best abilities.


### Conclusion

> "Feedback is everything. Most of the time you're wrong, and
> feedback is the only way to realize your mistakes and help you
> become less wrong." Nathan Marz

I like this quote, my experience makes me believe in every word
of it. Most of the code we write is wrong, we are not smart enough,
good enough and experienced enough to keep in our heads all the details required to
make it correct.

We need feedback - we need test output, QA team, REPLs to play with the
code, compiler type errors, and we need to know where we need this feedback the most.

In my opinion boundaries are a place where we should be most careful,
we should use guards, asserts, proxies and whatever else we have at our disposal
to make sure that connections between parts of our software work correctly.





