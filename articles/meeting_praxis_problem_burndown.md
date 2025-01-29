# Solving Problems in Engineering Organizations

This is a short checklist / framework for guiding meetings where some engineers sit together and try to solve a Problem.
This can be very light (2 people informally recording the results in a chat app) or very heavy (10 people in an incident response with collaborative document creation and a Situation Leader handling the process of stepping through the meeting).

Use this to make clear, step by step progress against a Problem and generate a written record of our attempt to solve it.

## Specify exactly what the problem is.

First we get a clear definition of exactly what the problem is. Software is wildly complex. If lucky, the surface of the system that needs to be analyzed to understand the problem is small. If unlucky, it is large.  

Either way, we consider this step complete when we have a written specification of the problem that renders it understandable.

## Specify what threat of the problem is.

What's the damage potential of the threat? How bad is it already? How bad could it potentially be? Quantify this! What's an ideal fix? What's the const benefit tradeoff on the path towards an ideal fix?

We consider this step complete when we have a precise threat description and a cost benefit analysis of a few ways to fix the threat.

## How did the person that called the meeting for help with the problem get past the threat of it? What made them call in assistance?

How did they survive the threat? How did they recognize the size of the problem? Do we have hacks in place to buy time we need to fix? Do we have damaged software that will catastrophically break soon?

We consider this step complete when we know the current state of the problem from one person materially and recently affected by it and we have a written description of what we've done to reduce problem impact so far.

## Who else has hit the problem and gotten past its threat? How did they do so?

Now we find others in the org that have had to deal with the problem and survive the threat.  How much damage did it do? How'd they reduce problem impact? What's the ongoing cost?

We consider this step complete when we know everyone else that has hit this problem, and have a written record of what they did about it.

## The information is gathered: Now we solve the problem.

We know the problem, the threat of the problem, who it has hit, and how they've so far dealt with it.

We now explore the cost/benefit of fixing it through various methods, analyze with computations the damages, the underlying systems repairability, and ongoing human toil cost.

We consider this step complete when we have a written plan of action informed by a cost benefit analysis on how to fix or ameliorate the problem.  We record this in a doc (along with the rest of the artifacts of this meeting) so the next time the problem comes up we have a better starting place.

## We have a plan of action: execute

Now for plan execution. After plan execution measure the results, decide if they fix is good enough, and either repeat this cycle or continue to some other engineering task.
