---
layout: post
title: "Putting Out A Software Fire"
date: 2017-12-29
---

We did fire drills most mornings when I worked on tour boats on the Chicago river.
I think drills are mostly for building muscle memory so that in the event of a real fire, you don't have to think about what you're doing, you'll just do it.
But drills also prepare you mentally.
The captain yells something at you and you have to learn not to freeze up, to think clearly in a tough situation.

We sometimes call solving urgent software issues "putting out fires."
I've never worked on software that's embedded in life-supporting or life-saving equipment, or in dangerous equipment like cars, nor have I ever put out an actual fire, but I found myself in charge of solving an urgent software issue recently and it was stressful.
The product owner was stressed, I was stressed, and, like a fire, the more time went on, the worse it got.
I'd like to share some things I learned from that experience.

## Give it an honest shot, try to own it ##
It's easy to feel like you don't know what you're doing, or like you don't have the necessary experience or context.
Maybe you've always been on teams where fixing production issues was someone else's job, or maybe you're new on the project.
Realize that, despite these shortcomings, you may actually be the best person to solve the problem right now.
Just because you've never put out a fire before, doesn't mean you can't put out this fire.
Just because you're new to this project, doesn't mean you can't learn what it does and how it does it.

When I used to get nervous before music performances, I'd ask the question, "Is there anyone in the room that's more prepared to play this concerto than me right now?"
Usually the answer was no.

## Find people to help you ##
If solving the problem has fallen to you, it probably means that if better-suited people exist, those people are busy.
But this doesn't mean you can't get help from them.
And it doesn't mean you can't find less-suited people to help you.
Finding a person to pair with helped me immensely.
We bounced ideas off of each other and I quickly realized that, while this person didn't know the project, they did have a lot of experience fixing production issues elsewhere.
We also spent some time talking to one of the projects previous developers, and a new person at the stakeholder's office who had time and was willing to help.

## Broad strokes, try to rule things out and hone in on the problem ##
There is this thing you can do when something doesn't work.
You can simplify the problem to see if you can find its root.
"Does it work when I take away X?"
If so, then, "Does it work when I also take away Y?"
If not, then, "Do other things that use Y work properly?"
I think this is what people call "troubleshooting."
You probably already do this in your day-to-day.

## Write everything down ##
This is so important.
Under normal circumstances, we can only keep so many ideas in our heads at once before we lose the chain and have to start over.
In stressful situations, it can be overwhelming.

Write down what the problem is.
Write down the things you know, and write down the things you learn along the way.
Write down the things you rule out, so you don't do the same work twice.
Write down your thoughts so you can come back to them later.
While in the middle of approaching the problem from one angle, you may think of other approaches.
Write those down too.

## Keep the stakeholders informed ##
I was lucky to have a pair who had experience in this type of situation.
The point where this became obvious was when he said, "Well, we've been at this for about an hour and a half. Time for a status update."

It's tempting to keep things to yourself until you can tell your stakeholder the good news.
I mean, who wants to tell their boss that they *still* haven't solved the issue?
Realize that these people are concerned, though, and that they're basically in the dark until you tell them something.
Think about sitting on an airplane runway, waiting for an hour before takeoff.
How much nicer is that experience when the captain tells you what the problem is and gives you regular updates on what the crew is doing to solve it?

Even if your stakeholder didn't write the code, they likely have more context about the project and its customers than you do.
Hearing what you're stuck on and what you've tried so far gives them an opportunity to help.

## Limit the rabbit hole ##
If you're new to the code, it can be tempting to want to understand everything about it in your search for the problem.
Realize that this may not be possible in the amount of time you have.
If you have a few ideas about what the issue might be, try spending 15 minutes on each.
Use a timer.
If nothing else, it will be a reminder to focus again on whatever the problem is.

## Take breaks ##
At one point, my pair and I realized that our progress had slowed down and also that it was past our normal lunch time.
Taking breaks to eat, drink, walk around, and talk about other things can help you come back to the problem with a clear head.
You may also find that an idea comes along during one of these breaks (write that baby down).

There were times near the beginning where I was stressed.
But it did feel great to see progress in our work and to finally solve the issue.
Hopefully this post has given you some ideas to try when putting out a software fire.
