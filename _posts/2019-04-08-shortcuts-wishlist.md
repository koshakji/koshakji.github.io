---
title: My Shortcuts Wishlist - The Little Things
layout: post

---

Since Apple introduced Siri Shortcuts and the Shortcuts app (nothing confusing about that naming scheme!), a lot have been said about what features they should add.
There are a few common demands: Siri Shortcuts input to create flexible shortcuts, scheduling Shortcuts to run at specific times and folders/tags to organize long lists of Shortcuts in the app.
Don't get me wrong: these wishes are awesome features that I would love to see in the next big update of the Shortcuts app, however, they are all big changes that, in some cases, need developer support and API changes, which makes them unlikely to happen without some on-stage time in some Apple event, most likely WWDC.

What I'm interested in getting into right now are the features that don't need that kind of attention, the features that can be added in a small point release, the features some people could maybe not even notice if they're not really paying attention. I started thinking about this after an interesting conversation with [Matthew Cassinelli](https://www.matthewcassinelli.com/) on the Shortcuts User Group Discord server and here are a few small features I would love to see



Disclaimer: I know almost everything on my wishlist is doable in the current Shortcuts app, but the implementation is tedious and could be much simpler.

## Better Loops

Sometimes it's hard to know how many times exactly you want to repeat actions in a Shortcut, which is why some kind of while loop is a thing that exists in (probably) all programming languages.
I know this could lead to some problems like infinite loops if the loop condition always evaluates to true, but I don't think it's a good idea to limit the power of the app just to protect the users from their own mistakes. User mistakes are always a possibility in an app that hopes to be as powerful as Shortcuts, there's just no avoiding it, the best the team could do is try to provide useful error messages to inform users of what could be causing  the problem.

The current possible workaround that comes to mind is using the Repeat action with a very large number of iterations, but the problem is, as far as I'm aware[^1] , there's no way to break out of a loop and continue the execution of subsequent actions in the Shortcut.


A break action would be very nice, but wouldn't really replace the need for the while loop, you would still be limited to how big the number of repeats you set, which could be unknown in some cases.

As suggested by [Rosemary Orchard](https://rosemaryorchard.com/), it's also possible to do while loops and breaks using a recursive Shortcut that uses the Run Shortcut action to run itself, but I think that's too complex for what should be a simple thing.

[^1]: it's possible to have the loop execute nothing if a condition is met but that's a hack that wastes time and cpu cycles for no good reason

## Boolean Logic

This feels like it should've been done a long time ago: instead of having to combine a bunch of if statement actions, it should be possible to create a single if statement with complex boolean logic. It would make my life so much easier if I could have AND, OR, and NOT operators between two conditions in a single if statement.
This could be as simple as a text field where the user can type arbitrary text which is then parsed by the app to create the condition, which would probably be easiest for advanced users, but could lead to a lot of errors when used by more novice users, which is why my solution is a bit more involved: I would love to see a Condition action, somewhat similar to the Find Reminders/Calendar Events/Music/Photos action and the Filter Images/Files/Articles actions. The user can easily select whether all the compounding conditions have to be true (an AND operator), or if one is enough (an OR operator), and every comparison operation could also have a NOT operator version just like the current "is" and "is not" comparisons.

![The current find and filter actions](/assets/2019-04-08-shortcuts-wishlist.png)

One of the benefits of this kind of action is that the current if statement action would stay almost the same, only possibly adding "is True" evaluation next to the "Contains", "Equals", "Is Greater Than" and "Is Less Than".
This system could also be extended by having the comparison operators be specific to the variable type, for example showing "Contains" for text and lists, and "Is Greater Than" for numbers.

## Mathematical Expressions

In the same spirit, creating a Shortcut that has a bunch of mathematical calculations gets tedious very quickly because you need to add a Calculate action for every single mathematical operation.
I'm not sure how the interface for this would look like, but a text field wouldn't be the worst idea, I believe a lot of people are familiar with the look of a bunch of mathematics and I'm not sure any other way would be nearly as efficient.

## Block Folding

This one is pretty simple and doesn't require any new actions, just a little button that hides the block of actions that go between something like the if action and the end if, hiding the group of actions that are supposed to be conditionally run, repeated within a repeat loop, or the actions corresponding with an option of the "Choose from Menu".
It's not a big thing but it would make long Shortcuts more readable and easier to deal with especially on the smaller screen of iPhones. 

## Markdown Actions

The app already has a way to parse Markdown to convert it to Rich Text and HTML, so why not an action that parses markdown and extracts a user specified part from the markdown?
The most obvious example would be extracting specific-level headers, e.g. extracting level-one headers, selecting the first to be the name of a file or the title of a blog post would be awesome. But other cool use cases could include extracting code blocks and block-quotes, lists and paragraphs without having to fiddle around with regular expressions.