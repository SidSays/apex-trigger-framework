# Simple, Scalable & Maintainable apex framework

The last thing you want to hear is "an apex framework", please bear with me or watch your favorite show instead !

## Description
In my 15 years of implementing salesforce for highly complex multi orgs to simple orgs, this framework has helped me most in terms of time saving, ease to educate developers, keeping regression impact minimum yet empowering future scalability and ease of deployments.

> For me, the pros far outweigh the cons.

## Why?
- You lead a team of mixed bag of differntly skilled developers, admins and everyone came from some other technology
- The team changes due to attrition or vendor changes and hence its difficult to follow documentation and standards they left behind
- Everyone has been following some framework which isn't fully understood by every architect and developer
- You need something simple, efficient, scalable where new functionlity is always kept separted to avoid regression impacts
- You know exactly to locate where a problem is arising, easy to identify a small helper class instead of a HUGE MAMMOTH Handler class
- Your org has multiple business units with different release dates and different admins/developers
- Your deployments can be short and efficient because they're deploying specific helper classes and their test classes

## How?
- A switch (Custom Metadata) to turn the trigger for an object ON or OFF, e.g. data migrations
- One trigger per object
- Trigger calls a Handler Class (Which implements an interface)
- Handler class deals with the events (Before, After) and invokes individual Helper classes for each Business Unit
- Handler class also contains a common Helper class for each Business Unit

