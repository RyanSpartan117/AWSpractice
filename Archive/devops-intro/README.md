# Introduction to DevOps

## Timings

45 - 60 Minutes

## This lesson covers

* Seriously, what is it?
* The four pillars
* The risk register

Explaining DevOps is a tricky thing to do because it means different things to different people. Here's my attempt at making it clearer. 

For many years there was a wall between developers and the people who handled company infrastructure. The guys who setup servers and managed databases would also often be in charge of fixing your PC or installing phone lines. And they often wouldn't be particularly interested in supporting the development of new software.

The developers would finish building their software and would effectively throw their code 'over the wall' to the operations team to get it deployed because they weren't allowed to touch the servers themselves. You can imagine the problems this would cause. The operations team didn't understand what the developers had built and the developers hadn't thought about where it was going to go. 

To solve this problem teams would try to learn a little more about what the others did so they could forsee problems and communicate better between the teams. This joining of Developers and Operations is known as DevOps.

In a nutshell DevOps teams are responsible for tying all the other teams together. They provide the tools and the areas in which to work. 

The biggest problem is that at the time of writing this lesson not many people agree on what the best way to do this is.

## Ease of use, Flexibility, Robustness and Cost

For a DevOps engineer the tools are less important than problem you are trying to solve. Broadly speaking the challenges we face will fall in to four categories:

### Ease of use

Because the other teams will be using the tools that we build to do their jobs it is our responsablilty to make sure that they are fast and easy to use. It is not uncommon for developers to "rebel" and stop using your carefully crafted system because it's too slow or too buggy. This has massive implications further down the line. 

So we need to build tools that are easy to use first of all!

### Flexibility

Software, tools and development practises have always changed at an incredible rate. Many industries currently face the problem that they have spent years embedding themselves in technologies that are massively out of date. This is called "Vendor lock in". 

We need to make sure that everything we build can easily be changed and updated as the needs of the business change.

### Robustness

The developers may be responsible for the code and the SDETs may be responsible for testing it, but it's the DevOps team that will ultimately make sure that the product stays live. To do this we will try to make it as close to impossible as we can for anyone to accidently or deliberately break or overwhelm our system. This should include security testing too!

### Cost

This is an area that is often overlooked. But technology costs money. It is our job to make sure that we are being as efficient as we can with the tech we are using. For example should we use the most powerful ( but expensive ) server available or can we get by with something smaller. Should we shut down testing platforms when they're not being used or is it more cost effective to leave them running. All these things should be taken in to account.

## The Risk Register

We have learned a lot about Agile teams and often DevOps engineers are included in this way of working. But in addition to the agile backlog DevOps teams will often use a risk register to highlight areas of highest risk in their systems.

The risk register helps us to organise our priorities with a combination of how likely something is to happen alongside the impact it could have on the business.

A typical risk register might look like this:

| Description              | Chance of occurence | Potential Damage                 | Risk       |
|--------------------------|---------------------|----------------------------------|------------|
| Dev Environment broken   | Medium              | Developers can not work          | Low/Medium |
| Testing server broken    | Medium/High         | New code cannot be tested        | Low/Medium |
| Automated testing broken | Medium/High         | New code cannot be tested        | Low/Medium |
| Jenkins server broken    | Medium              | New code cannot be pushed live   | Low/Medium |
| Production server fails  | Medium              | Loss of revenue                  | High       |

You can see in this table that there are somethings that are highly likely to occur but that have a low impact on the business. This can also change depending the on business that you are working for! A bank might have a high chance of an attempted hack whereas for a small static site for a cab firm it would be much less likely.

We have to weigh up which items should get our attention first. Anything with a high chance of occurrence and a high impact on the business is likely to be at the top of list!

## Summary

You just:

* Learned a little about the history of DevOps
* Learned the four factors we must consider when building a sytem
* Saw how to use a risk register to decide priorities





