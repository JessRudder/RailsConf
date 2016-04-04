- intro
  - used to work at StarBucks (RubyBucks)
  - due to how the system was set up, people would easily steal drinks that didn't belong to them
  - caffeine creeper grabbing stuff when no one was looking
- sql injection (Squeeeeeelie)
  - what it is
    - submitting sql as part of your query in the hopes of getting back data that doesn't belong to you
  - Appears to have first been documented in 1998 by Jeff Forristal
    - companies were just switching over from MS Access dbs to MS SQL dbs
    - found you could 'piggy back' commands to execute sql requests that the application developer might not have expected
  - Open Web Application Security Project (OWASP) places it at the top of their list of vulnerabilities in the "Top 10" report
  * There are scripts that crawl websites looking for forms and, when they find them, they automatically submit the forms with inputs that could cause a sql error
    - Troy Hunt (founder of haveibeenpwned.com) has a video showing him teaching his then 3-year-old to do a SQLi attack using a popular program (with a GUI and all)
  - examples (http://codecurmudgeon.com/wp/sql-injection-hall-of-shame/)
    - 11/2015 - personal data of 5 million parents and 200,000 kids
      - includes names, email addresses, passwords, and home addresses of 4,833,678 parents 
      - includes the first names, genders and birthdays of more than 200,000 kids
      * kids can be linked to parents which provides first, last names and addresses for kids
    - 10/2015 - 15gb of user data from Patreon (including emails, passwords, campaigns they supported, etc)
      * saving grace was passwords were incrypted using bcrypt which is time consuming to crack
    - 8/2014 - Russian hackers steal over 1 billion passwords from 400,000 sites (ranging from fortune 500 to very small)
    - 10/2013
    - 10/2012 - thousands of personal records from 53 universities (including Harvard, Stanford, Cornell, Princeton, Johns Hopkins, the University of Zurich and other universities around the world)
      - Student info is highly saught after due to their pristine credit records and the fact they don't watch their credit scores
      * At least there's one good side effect of crushing student debt. By the time you graduate, no one wants to steal your identity any more.
    - 6/2011 - sonypictures.com lost over 1,000,000 users data including passwords that were reportedly stored in plain text
    - 4/2011 - PlayStation Network hack exposed the names, birthdays, email addresses, passwords, security questions, and possibly credit card details, of all PSN users.
      * passwords and other personal information stored in plain text
    - 5/2009 - hackers get access to at least two sensitive web servers maintained by the U.S. Army and redirect visitors to anti-U.S. military sites
    - 8/2005 through 7/2012 american Albert Gonzalez and 4 co-conspirators steal over 160 million credit card details
      * charged $10 for each stolen U.S. credit card number and associated data; about $50 for each European credit card number; and about $15 for each Canadian credit card number - with discounted pricing offered to bulk and repeat customers, the indictment says.

- activerecord
  - not required, but used in majority of rails apps that are backed by a database
  - vulnerabilities due to need to allow flexibility
    - coffee shop could have required people to show id to pick up their drinks but it would be slow and complicate things for all but simple customers (e.g. one person grabbing their coffee and their friends)
    - allows you to use raw sql to craft more complex queries
  - queries to go over
    - Barbecue.find(1)
      - safe, no chance of sql injection
      - ActiveRecord::RecordNotFound will be raised if one or more ids are not found.
    - Barbecue.find_by(name: "Arthur Bryants") and Barbecue.find_by(type: "Burnt Ends", dads_rating: 5)
      - safe, no chance of sql injection
      * What is AR doing here?
        - when passed in as a hash, AR escapes special characters and treats the entire thing like a string so even if you pass in some SQL it won't execute as your ```'); DELETE FROM Employees --``` becomes ```"'''); DELETE FROM Employees --'"``` which is all nice and escaped
          - evil_input = '"\';%#{}\"foo' becomes "'\"'';%\#{}\\\"foo'"
    - Barbecue.find_by('name ILIKE "Oklahoma"') or Barbecue.where('name ILIKE "Oklahoma"')
      - not safe!
    - Barbecue.find_by('name ILIKE ?', "Oklahoma") or Barbecue.where('name ILIKE ?', "Oklahoma")
    - Barbecue.where(expensive: false).group(params[:group])
      - params[:group] = "location UNION SELECT * FROM barbecues"
      - should return inexpensive barbecue joints grouped by their location. Instead it will return the full list of all barbecue joints in the database.

- outro
- questions
  - NO THAT'S A DUMB QUESTION AND I WILL NOT ANSWER IT
