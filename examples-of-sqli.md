```ruby
  def search
    query = params[:search]   @colleagues = find(:all, :conditions => "name LIKE '%#{query}%'")
  end
```
Raw "query" being injected straight into the database.

```
  '); DELETE FROM Employees --
```

Injecting that would then close out the initial search and tell it to delete everything from the table.

```
  SELECT "employees".* FROM "employees" WHERE (name LIKE '%'); DELETE FROM Employees -- %’)
```

NOW YOU HAVE NO EMPLOYEES!

That works because someone knows the table name already.  But how do they figure that out?

```
  ‘ OR EXISTS (SELECT 1 FROM Users) AND ‘’=’
```

They could keep running this command and switch out "Users" for any other table until they hit one that doesn't throw an error.  No error == the table exists.

How do you fix it?

```
  def search
    query = params[:search]
    @colleagues = find(:all, :conditions => ['name LIKE ?', '%' + query + '%'])
  end
```
 Send in a parameterized query (the question marks).

 That magic happens in [active_record/sanitization.rb](https://github.com/rails/rails/blob/master/activerecord/lib/active_record/sanitization.rb) with help from [ active_record / connection_adapters / abstract / quoting.rb.](https://github.com/rails/rails/blob/master/activerecord/lib/active_record/connection_adapters/abstract/quoting.rb).  Basically it escapes all of the special characters so the query is sent in as nothing more than a simple string.

 1) Sanitize User Input - escape the special characters

 2) Parameterized Queries - sql statement is sent to the database with placeholders for the question marks.  Database parses the statement and comes up with a query plan that is cached for future use.  A token is sent back to the app representing that query plan.  When the actual values come through, the app sends the query token and the values to the database.  This adds extra security because the database won't veer off the preset path.  Info from the user is treated as simple statements and can't change the type of queries that are being made.
  * This is handled differently by each database - some don't support it - so the database adapter determines what to do when you send in a parameterized query.

  NEVER TRUST ANY USER SUPPLIED DATA

* Walk through of finding out database info using sql injection - http://www.unixwiz.net/techtips/sql-injection.html#addnew
* Blog post about basics of activerecord protections from sqli - https://blog.8thlight.com/adam-gooch/2013/01/04/protect-yourself-from-sql-injection.html
* Some information about vulnerabilities for noSQL databases - https://www.owasp.org/index.php/Testing_for_NoSQL_injection
* Exampes of SQL injection in Rails - http://rails-sqli.org/
