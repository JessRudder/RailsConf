**40 Minutes including all Q/A**

Did you all enjoy lunch?  Anyone go out and grab a coffee during the break?  Yeah...some of you?

Many lifetimes ago, I worked as a barista at a popular coffee shop.

It was a mid-sized neighborhood store with a mix of traffic coming from the surrounding homes and businesses.  We were actually right across the street from one of the big game studios so we'd always have a spike in business right before the next Call of Duty was supposed to be finished.  It was a nice mix of customers and most of the regulars were great....but there was one....(there always is).

Susie <put an asterisk with name changed to protect the guilty> would come in 3-4 times per week...and she always had the same order.  "Double espresso please"....

I'd make small talk as we finished the transaction...

"How's it going Susie?"

"*sigh* Oh...you know....not so well...."

She'd launch into some tale of woe...things that just weren't going well (mind you, she was a customer that was in 3-4 times per week and it was always the same deal).  Then, right as I'd hand her the change, she'd perk up a bit and say, "But if you made that a triple latte instead, it'd be better..."

Every. Single. Time.

She'd wait for the register to close...signaling that the transaction had officially come to an end.  Then she'd try to sneak something else in there.

Which brings me to our topic - safety and security in coffee shops.

Wait....that doesn't sound right....hmmm...maybe save that for a java conference.

Well, we're a room full of (mostly) web developers.  So, lets talk about security in web apps instead.

At the risk of stretching that coffee shop metaphor a bit too far, I'd argue that our web applications have a vulnerability that's very similar to the one Susie was exploiting.

SQL injection.

We'll go into more detail in a minute, but, in brief, it's when someone closes out a legitimate transaction with your database then immediately tries to sneak more SQL commands in there so they can interact with your database and walk off with something a whole lot more valuable than coffee.  And, I know what you're thinking...."We're developers!  There's nothing more valuable than coffee."  But, in this case, there is...it's your customer's private information.

When we talk about security vulnerabilities, we expect that we're talking about something new.  A vulnerability that was just discovered in the past few weeks.  Something like Heartbleed where it's announced publicly the same day a patch is released.

SQL injection though...it's old enough to vote. The first public mention of it seems to have been by Jeff Forristal in an online hacker magazine called "Phrack" in 1998.  Jeff, who went by the hacker name of Rain Forrest Puppy...

Actually, let's pause here for a moment.  While researching this I came across a lot of stories and news articles sporting people's hacker names and I realized - I haven't got a hacker name.  So I did what any reasonable web developer would do...I googled "Hacker Name Generators".  I tried out a few different options (including one dubious one that insisted it needed to know my mother's maiden name in order to generate my hacker name...nice try) and I was the proud owner of this doozie:

Fire 4cid

You know it's a legit hacker name because they used a 4 instead of an A.

So, Rain Forrest Puppy had discovered that SQL databases (which were just starting to replace the then more popular Access databases) allowed batch commands - the ability to send multiple requests through to the database at a time.  NBD because multitasking is good, right?  Perhaps I want to be able to select all employees that are engineers then average their chips_consumed (or, if you're joining us from places that speak that other english - chips_consumed).  Which, in both cases is...high.  We'll leave it at that.  This is all well and good in closed systems.  However, when you have input coming from outside users heading straight into your database...well, things get a little dicy.

A shady character...like this one....could user your innocent little web form to gain access to parts of your database you didn't want them to have by piggy backing their own SQL requests onto your intended request....like so.

You kindly provide your users with the ability to search for a restaurant even when they only know part of the name:

SELECT "restaurant".* FROM "restaurants" WHERE ("name LIKE '%user_input%');

And shady mcshadester searches for the following restaurant (which doesn't sound so great to me):

'); DELETE FROM Restaurants --

Which gives us the following SQL query:

SELECT "restaurant".* FROM "restaurants" WHERE (name LIKE '%'); DELETE FROM Restaurants -- %’)

And, suddenly, your entire restaurant table is gone....not your table at the restaurant...your restaurant table in your database.  Though you should probably cancel those reservations because you're going to be spending the rest of the night restoring your database from backups.  You do have database backups, right?  Not really our topic today...but generally a good idea.

So, yeah...SQL injection....it has been on people's radar for at least 18 years and yet it regularly ranks at the top of OWASPs (Open Web Application Security Project) 10 most critical web application security risks.  And that is a top 10 list you don't want to be at the top of.

In fact, a quick scan of recent news will give you plenty of examples of SQL injection in action.

In June 2011 sonypictures.com lost the data of over 1,000,000 users, including passwords that were reportedly stored in plain text.  Ouch!

In October of 2012, hackers used SQLi to get the personal records of thousands of students from 53 universities including Harvard, Stanford, Princeton and other top universities around the world.  It turns out that student info is highly sought after due to a combo of pristine credit records and lack of watching their credit scores.  I guess that's one positive side effect of crushing student debt...by the time you graduate, no one wants to steal your identity any more.

In August 2014, Russian hackers stole 1.2 billion username and password combinations and 500,000 email addresses from 400,000 sites.  These weren't just small sites made by your cousin's best friend's uncle's kid who sort of knows how to code.  Some of these sites were for Fortune 500 companies.  And this wasn't pulled off by some uber-sophisticated, high tech gang of hackers - it's a group of less than a dozen men in their 20s in a small city in Russia...they actually started out as email spammers.

More recently in November 2015, an electronic toy company VTech was hacked and the personal data of 5 million parents and 200,000 kids (including the names, email addresses, passwords and home addresses of the parents) was taken.  What makes it worse is that the kids accounts can be linked to the parents which means you basically have first and last names and home addresses of a bunch of kids as well.

That's a small sample of some of the major hacks over the past 5 years.  There are so many more to choose from!  Hacks that affected the New York Times, Target, Sony (again), the U.S. Army and even the Department of Homeland Security!

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

My name is Jessica and I work at the Flatiron School.  I help build Learn - an online platform for teaching people how to code.  Part of our teaching philosophy is to have people build the tools they'll be using later - it helps to ensure that they understand what's happening under the hood.  In our web development program, our students have to work with SQL and build their own light-weight ORMs.  After suffering through this for a while, we introduce them to ActiveRecord - which seems like magic!

No more:

SELECT * FROM "restaurants" WHERE type = "bbq"

Instead:

Restaurants.where(type: "bbq")

And because there's so much magic involved, it's easy to to think that everything has been covered.  The only thing you need to worry about is remembering whether the rails convention is for a model to be singular or plural....but it's a bit more complicated than that. 

Alright - we're at Rails Conference and you came to a talk titled "Will It Inject? A Look at SQL Injection and ActiveRecord"...and congratulations for making it all the way to the title slide.  And, given this title, I think it's safe to assume you're interested in hearing about this from an ActiveRecord perspective.

But having someone yack at you about security (especially right after lunch) can be boring...so instead we're going to play a little game I like to call "Will It Inject?!?"  I'll show you an ActiveRecord method and you can tell me whether or not you think it's vulnerable to SQL injection.  Feel free to play along if you're watching this at home...just shout it right out at the monitor.  I won't judge.


````
Bunch of different AR Methods
Poll the audience to keep them engaged
Give some of them silly names - I like to call this one 'the big boss'
Throw a trick one in there "Will it inject?  No!  This isn't even ActiveRecord...it's Python.  Just making sure you're awake."
````

Some of you may be thinking, if people are aware of these vulnerabilities, why hasn't it been fixed?  I'll go back to my hotel room tonight, write the patch and open a PR!

If only it were that simple.  This is yet another example of that age old tension between freedom and security.  Those vulnerabilities are there to allow us flexibility.

The coffee shop I worked at gave baristas wide lattitude on bonus shots and upgrades to allow us to surprise and delight customers.  They could have locked things down with a policy that said, "No one ever gets anything they didn't pay for."  And that definitely would have meant that Susie's "I'd like a latte but I want to pay for a triple espresso" shtick would never work.  But we would have lost a lot of important, magical moments with other customers.

Similarly, there are times when the data we need from the database is complicated and being able to drop down to raw sql is the only straightforward way of getting it.  If ActiveRecord didn't allow this flexibility, it would be less and less useful as our apps got more and more complex.  We'd spend a lot of time writing our own methods from scratch and if we've got time to do that we've probably got time to just make sure that we're not leaving our ActiveRecord methods vulnerable.


It's not something we usually have to pay attention to because ActiveRecord has done a great job of protecting against it.  However, if you're not aware, these sorts of things can sneak up on you.  Next thing you know, your company's on the nightly news.  With just  little bit of prevention, you can make sure you stay out of the new.

Here are some resources:
