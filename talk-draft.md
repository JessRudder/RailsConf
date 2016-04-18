**40 Minutes including all Q/A**

Many lifetimes ago, I worked as a barista at a popular coffee shop.

It was a mid-sized neighborhood store with a mix of traffic coming from the surrounding homes and businesses.  We were actually right across the street from one of the big game studios so we'd always have a spike in business right before the next Call of Duty was supposed to be finished.  It was a nice mix of customers and most of the regulars were great....but there was one....(there always is).

Susie <put an asterisk with name changed to protect the guilty> would come in 3-4 times per week...and she always had the same order.  "Triple espresso please"....

======= REMOVE THIS PART ======
It seems that someone had discovered a vulnerability in the Ruby Bucks system. There was a brief moment between the time where I set the drink out and when Bill picked the drink up when nobody was watching the drink.  In that moment, it was simple for someone sketchy caffeine addict to abscond with coffee that didn't belong to them.

======= END REMOVE =========
Which brings me to our topic - safety and security in coffee shops.

Wait....that doesn't sound right....hmmm...maybe save that for a java conference.

So...we're web developers which is awesome because we don't have to worry about people stealing 

Our web applications have similar vulnerabilites.  A brief moment in time, where, if we're not careful, no one is minding the database and shady characters can walk off with something more valuable than coffee.  And, I know what you're thinking...."We're developers!  There's nothing more valuable than coffee."  But, in this case, there is...it's your customer's private information.

------- End Intro -------

When we talk about security vulnerabilities, we expect that we're talking about something new.  A vulnerability that was just discovered in the past few weeks.  Something like Heartbleed where it's announced publicly the same day a patch is released.

SQL injection though...it's old enough to vote. The first public mention of it seems to have been by Jeff Forristal in an online hacker magazine called "Phrack" in 1998.  Jeff, who went by the hacker name of Rain Forrest Puppy...

Actually, let's pause here for a moment.  While researching this I came across a lot of stories and news articles sporting people's hacker names and I realized - I haven't got a hacker name.  So I did what any reasonable web developer would do...I googled "Hacker Name Generators".  I tried out a few different options (including one dubious one that insisted it needed to know my mother's maiden name in order to generate my hacker name...nice try) and I was the proud owner of this doozie:

Fire 4cid

You know it's a legit hacker name because they used a 4 instead of an A.

So, Rain Forrest Puppy had discovered that SQL databases (which were just starting to replace the then more popular Access databases) allowed batch commands - the ability to send multiple requests through to the database at a time.  NBD because multitasking is good, right?  Perhaps I want to be able to select all employees that are engineers then average their bags_of_chips_consumed column.  Which is all well and good in closed systems.  However, when you have input coming from outside users heading straight into your database...well, things get a little dicy.

A shady character...like this one....could user your innocent little web form to gain access to parts of your database you didn't want them to have by piggy backing their own SQL requests onto your intended request....like so.

You kindly provide your users with the ability to search for a restaurant even when they only know part of the name, like so:

SELECT "restaurant".* FROM "restaurants" WHERE ("name LIKE '%user_input%');

And shady mcshadester searches for the following restaurant (which doesn't sound so great to me):

'); DELETE FROM Restaurants --

Which gives us the following SQL query:

SELECT "restaurant".* FROM "restaurants" WHERE (name LIKE '%'); DELETE FROM Restaurants -- %’)

And, suddenly, your entire restaurant table is gone....not your table at the restaurant...your restaurant table in your database.  Though you should probably cancel those reservations because you're going to be spending the rest of the night restoring your database from backups.

So, yeah...SQL injection....it has been on people's radar for at least 18 years and yet it regularly ranks at the top of OWASPs (Open Web Application Security Project) 10 most critical web application security risks.

In fact, a quick scan of recent news will give you plenty of examples of SQL injection in action.

In June 2011 sonypictures.com lost the data of over 1,000,000 users, including passwords that were reportedly stored in plain text.  Ouch!

In October of 2012, hackers used SQLi to get the personal records of thousands of students from 53 universities including Harvard, Stanford, Princeton and other top universities around the world.  It turns out that student info is highly sought after due to a combo of pristine credit records and lack of watching their credit scores.  I guess that's one positive side effect of crushing student debt...by the time you graduate, no one wants to steal your identity any more.

In August 2014, Russian hackers steal 1.2 billion username and passord combinations and 500,000 email addresses from 400,000 sites.  These aren't just small sites made by your cousins best friends uncles kid who sort of knows how to code.  Some of these sites were for Fortune 500 companies.  And this wasn't pulled off by some uber-sophisticated, high tech gang of hackers - it's a group of less than a dozen men in their 20s in a small city in Russia...they actually started out as email spammers.

More recently in November 2015, an electronic toy company VTech was hacked and the personal data of 5 million parents and 200,000 kids (including the names, email addresses, passwords and home addresses of the parents) was taken.  What makes it worse is that the kids accounts can be linked to the parents which means you basically have first and last names and home addresses of a bunch of kids as well.

That's just a sample of some of the major hacks over the past 5 years.  There are so many more to choose from!  Hacks that affected the New York Times, Target, Sony (again), the U.S. Army and even the Department of Homeland Security!

So what's the deal, is there some elite Hacker Havard out there teaching the world's greatest hackers how to sneak unwanted SQL commands into our databases like sneaky little code ninja?

In a word....nope.

SQL injection is common for two reasons.

The first is that it's easy to automate.  There are scripts you can buy that will just bounce around the internet testing web forms and URLs to see which ones are vulnerable.  Once a site is found, the same basic techniques can be used to get access to your data.  Troy Hunt (a web security expert and founder of haveibeenpwned.com) actually has a video showing him teaching his then 3-year-old to do a SQLi attack using one of the most popular programs.  This thing has a gui and everything...you don't even have to know how to use the command line!

The second is that it keeps working.  Hackers continue to use it because even though we've known about it for 18 years, they're still getting gigs and gigs worth of valuable personal data from our web apps.

So, how do we put an end to this?  It's a bit tough to make SQLi harder to automate.  I suppose you could give your tables weird names like "X[08?gY&" instead of "Users" so people can't guess your table names but that only protects you from some injection and will drive the developers on your team insane.

The easier route is making sure it doesn't work.

Awesome.  This talk is over!

Wait...you want to know how to make sure it doesn't work.

Fine...if you insist.

Well, we're at Rails Conference and you came to a talk titled "Will It Inject? A Look at SQL Injection and ActiveRecord" (which is a miracle...with a title like that I expected to have no one but my dad in the room) so I think it's safe to assume you're interested in hearing about this from an ActiveRecord perspective.

But having someone yack at you about security can be boring...so instead we're going to play a little game I like to call "Will It Inject?!?"  I'll show you an ActiveRecord method and you can tell me whether or not you think it's vulnerable to SQL injection.  Feel free to play along if you're watching this at home...just shout it right out at the monitor.  I won't judge.


````
Bunch of different AR Methods
Poll the audience to keep them engaged
Give some of them silly names - I like to call this one 'the big boss'
Throw a trick one in there "Will it inject?  No!  This isn't even ActiveRecord...it's Python.  Just making sure you're awake."
````

Some of you may be thinking, if people are aware of these vulnerabilities, why hasn't it been fixed?  I'll go back to my hotel room tonight, write the patch and open a PR!

If only it were that simple.  This is yet another example of that age old tension between freedom and security.  Those vulnerabilities are there to allow us flexibility.  The coffee shop I worked at gave baristas wide lattitude on bonus shots and upgrades to allow us to surprise and delight customers.  If they had locked things down and said, no one ever gets anything they didn't pay for...well, her "I'd like a latte but I want to pay for a triple espresso" shtick would definitely not have worked.  But there were a lot of magical moments with other customers that would have been missed.  Similarly, there are times when the data we need from the database is complicated and being able to drop down to raw sql is the only straightforward way of getting it.  If ActiveRecord didn't allow this flexibility, it would start to be less and less useful as our apps get more and more complex.

