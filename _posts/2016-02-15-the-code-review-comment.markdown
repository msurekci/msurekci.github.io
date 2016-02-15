---
layout: post
title:  "The code review comment"
date:   2016-02-15
categories: code reviews, knowledge sharing
---

Along came a user story one day that required me to add a new table to the database. Little did I know it was going to change the way I thought about the way other developers thought when developing.

So, this particular table required 2 columns. ID and a name. Without giving it much thought we initially just set the types as UNIQUEIDENTIFIER (the ID was a guid) and nchar(100). All works fine. Off for a code review.

Oh, I have a comment and it says something along the lines of:

> "I don't think the name will be 100 characters long maybe it should be nvarchar"

I haven't spent much time with databases unfortunately, I didn't know the difference so I google'd it. 

The difference between nchar and nvarchar is nchar has a fixed length whereas, nvarchar has the length of the variable passed to it. So if I passed the value "Facebook" it would use less storage because it is not 100 characters. Clearly nvarchar is the better option.

But, then I thought I would dig deeper and look into char and varchar. There wasn't much of a difference when compared to nchar and nvarchar. The difference was that nchar and nvarchar accepted unicode. This meant that they also required twice the space of char and varchar.

So let's take a look at this:

| Type      | Value    | Bytes   |
| ----------|:--------:|:-------:|
| char(100) | Facebook | 100 	 |
| varchar() | Facebook | 8	 	 |
| nchar(100)| Facebook | 200 	 |
| nvarchar()| Facebook | 16 	 |


I started to think maybe nvarchar is not the best option and varchar is possibly better since it uses less storage space. I wasn't going to require unicode values either.

I didn't stop here however, I continued to try to find out the advantages and disadvantages of these types. I won't go into too much detail before we get sidetracked but the other topics were page splits and fragmentation. I came to the decision to use the type that I thought would use less bytes and that was varchar.

Before, I made that change and sent it back for a pull requests I wanted to consult some of my colleagues/friends as to what they thought was the right approach. This is where it got really interesting.

The first response:

> "Just use nvarchar. It's the most common type used for these types of situations". 

I wasn't satisfied with that answer. The most common way cannot be the reason it is correct. So I presented my findings about the space storage.

Next response:

> "It doesn't matter. What difference does it make if it uses a few extra bytes? It is going to take you more time to explain to people why you have not used nvarchar"

I was surprised by the response. It wasn't about necessarily the technical aspect but the people aspect. The conversation went on about how the company would lose money if you took into account the time I would spend explaining to people my choice rather than just doing the common way.

I still felt that it was not a valid argument and believed if I shared my knowledge with other developers then they may be enlightened as to the differences and when they make their own tables one day they can make a more informed decision. 

In all honesty, I was being stubborn and it wasn't going to make much difference. It was literally a few bytes I would save because there was only going to be approximately 10 rows in that table. Nevertheless, for me it was the right thing to do. Why would I use more storage space if I don't need it?

But, they also had a valid point. This discussion went on for maybe an hour with 5 or 6 people participating. Now if I had just done nvarchar no one was going to discuss this, go on with their work and give the company value.

Right and wrong is subjective. Both arguments are valid when you look at it from a certain point. 

Right now my right is to try and create the best possible solution and not shy away from explaining things as knowledge sharing is extremely important. It is why I learned the difference of these types; the code review comment.

P.S. I changed it to nvarchar because I'm not good at convincing people. But, I believe it should still be varchar because YAGNI.
