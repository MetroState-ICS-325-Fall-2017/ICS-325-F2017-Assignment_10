ICS 325 Fall 2016 - Assignment 10
=========================

Purpose
-------
* Use Silex, Twig, and MySQL to add features to a partially built blog application.

Resources and Examples
----------------------
* Check the D2L class notes section for how to install composer in your git repo and use it to install the necessary 3rd party packages.
* The official Silex [documentation](http://silex.sensiolabs.org/doc/master/)
* The official Twig [documentation](http://twig.sensiolabs.org/documentation).

Collaboration
-------------
You can work with 1 or 2 other students on this assignment. Make sure to mention who you worked with when you submit your assignment in D2L.

Prerequisites
-------------
Use git to clone your assignment 10 repo to your computer.  Then in PhpStorm, use `File->Open Directory` and select your local repo.  You will also need to install composer and use it to install the necessary 3rd party packages in your local repo.  In addition, you will need to make sure MySQL is running and that you've imported the `silex_blog_a10` database.

Instructions
------------
### Instructions to set up the code to run
First you need to clone your git repository to your computer.  Open GitKraken and make sure you are logged into your github.com account.  Next go to `File->Clone Repo`.  Select the `GitHub.com` icon.  A list of your repositories in github.com should pop up.  Select your assignment 10 repo.  If you want, change `Where to clone to` by clicking browser and selecting a folder for your git repo to be cloned into.  Finally, hit the `Clone the repo!` button.  The repo should now clone to your computer.

Next you need to set up PhpStorm.  We will be using the built-in PHP CGI server for this assignment.  To do so, first make sure you have the git repo open in PhpStorm by using the Open Directory menu item under File in PhpStorm (`File->Open Directory`).

Next install composer and use it to install all the necessary 3rd party packages.  Refer to the D2L class notes section for instructions on how to do this.

Next go to `Run->Edit Configurations...` Click the green `+` to create a new configuration.  Select `PHP Built-in Web Server`.  Change the name to `Assignment 10`.  Leave host as `localhost`.  Set the port to `8080`.  Set the `Document root` to the `web` directory in your git repo directory by clicking the `...` button next to the field and using the file chooser to select it.  Check the checkbox next to `Use a router script`.  Then select the file `index.php` in the `web` directory by clicking on the `...` button next to the router script field.  In the top right of the window there is a check box labled `Single instance only`, click on it to check it.  If there is a red ! icon near the bottom right of the window, click the `Fix` button and specify your PHP interpreter.  Once done, click `Ok` to exit the Edit Configurations window.  Next hit the green run button to start the PHP CGI web server.  Then go to your web browser, and enter this url [http://localhost:8080/](http://localhost:8080/).  That page lists existing routes and other info.

Next you need to set up MySQL.  Make sure that MySQL is running.  If you have XAMPP, open the XAMPP control panel and click the "start" button next to MySQL.  If you are in the database course, MySQL may already be running in the background on your machine.

Open MySQL workbench.  If there is a button in the top left that says "Local instance 3306" click it and you will be logged into the MySQL server.

If not, click the + button next to "MySQL Connections".  Name the connection "Local instance 3306".  Hostname should be "127.0.0.1".  Port should be "3306".  Username should be "root".  The password is blank if you use XAMPP.  If you installed MySQL for the database course, you may need to enter a password.  Hit okay and then open the connection by clicking on it.

Once you are in MySQL Workbench, go to File->Run SQL Script...  Select the `resources/silex_blog_a10.sql` script.  Hit the "Run" button.  If it asks for a password, you do not need one if you use XAMPP so just hit okay. Once it is done, look at the bottom left side of the screen where there is a panel that says "SCHEMAS".  Click the 2 headed arrow button next to it.  That will show you the schemas (databases) you have running.  Hit the refresh button (2 arrows in a circle) to see the `silex_blog_a10` database that was created by `resources/silex_blog_a10.sql`.  Click on the + next to `silex_blog_a10` to see the `users`, `user_roles`, `roles`, and `posts` tables.  To run queries against a database, double click it.  The database name will turn bold.  Any queries you execute in your query window will run against the bolded database by default.

### Assignment Work
The assignment is more complex compared to previous assignments.  It will have the weight of 2 assignments when used to calculate your homework grade.  The assignment is broken down into feature sets.  Each feature set is described and a grading table is provided.

#### Display Posts by Author
You need to create a new route for viewing all the posts by a specific author.  The route you should create is `/blog/author/{author_name}`.  Ensure that the route variable for the author name is properly escaped.  You should use the existing Twig template `list_posts.twig` to render the HTML output.  You may modify the Twig template however you want, but it should be shared among this new route and the routes that already use it.  You need to add a new method to the `PostRepostory` named `findByAuthor`.  It should take the the author's name as a string and then use the author's name to query the database for matching posts.  It should then return an array of `Post` instances found that match the query.  This code will work similarly to the `findAll` method, except that the SQL query will filter the results by author.  If no posts are found, then a 404 exception should be thrown just like in the `find` method.  You should name the route, and then modify the `list_posts.twig` file to use either a generated URL or path instead of the existing hard coded ones.  Similar to the `/blog/id/{id}` route, add an assert condition to only allow author names with letters, numbers, and spaces.

Points|Requirement
---|-----------
1 | create a new route
1 | properly escape the route variable for author
1 | use the existing Twig template (may modify it as necessary)
1 | new method `findByAuthor` added to the `PostRepository` class
1 | 404 exception is thrown if no posts are found
1 | name the route
1 | swap out the hard coded links with ones that use the URL or path generator
1 | use assert to only allow author names with letters, numbers, and spaces
**8**| **Total**

#### Edit a Post
Currently posts can only be created.  You need to add the ability to edit a post via a new route.  For example, `/posts/edit/{id}`.  Each field in the post can be changed by the editor, except for the created time and modified time.  The created time should always stay the same.  The modified time should be updated to when the post was last modified.  The updated data should be stored to the database.  The `save` method of the `PostRepository` class should be used to save modified `Post` instances.  Only users with the role `ROLE_USER` should be able to edit posts.  You should use a Twig template to render the HTML form used to edit the post.  The existing data for the post should be pre-filled into the form for the user.  You can use the existing `new-post.twig` template for this, and modify it to fit your needs.  You should use the Silex form builder and validators to build and validate the form.  You can use the existing ones found in the new-post controller.  You can either copy them to your new update post route or find a way to share the code between the 2 controllers.  Add an edit post link to the post views using the URL or path generator.  Modify all the set methods in the `Post` class to set `persisted` to false whenever an attribute of the `Post` instance is changed.  Then use the `persisted` attributed to skip updating a post in the database within the save method of the `PostRepository`.  So, in order words, if a `Post` instance is unmodified since it was loaded from the database, and it is given to the `PostRepository` to save to the database, don't issue a SQL `UPDATE` call to the database.

Points|Requirement
---|-----------
3 | each field can be updated by the editor
1 | modified time is updated
4 | data is updated in the database, must use the `save` method in the `PostRepository`
1 | only users with the `ROLE_USER` role can update posts
1 | use a Twig template to render the form with the existing post data
2 | Silex form builder and validators are used
1 | add a edit post link to the show all posts twig template
2 | modify all set methods of post to set persisted to false and then only save posts if `persisted` == `false`
**15**| **Total**

#### Delete Posts
You need to add a route that can be used to delete a post, for example `/blog/delete/{id}`.  Name the route.  Note that you are only required to create the route and ensure it deletes posts.  You do not need to modify the views to add links to delete posts unless you want to complete the optional points.  Only users with the role `ROLE_ADMIN` should be able to delete posts.  The row should be deleted from the database using a SQL query.  A new method `delete` should be added to the `PostRepository` class in order to perform the deletion.  It should accept a `Post` instance.  The controller that calls the `PostRepostory->delete` method should lookup the post by its id first using the `find` method and passing the returned `Post` instance to the `delete` method.   Add a delete post link to the post views using the URL or path generator.

Points|Requirement
---|-----------
1 | create a new route
1 | name the route
1 | only admins can delete posts
1 | data is deleted from the database, create a new method `delete` in the `PostRepository` to do so that accepts as a `Post` instance as input
1 | add a delete post link to the show all posts twig template
**5**| **Total**

Grading
-------

Points|Feature Set
---|-----------
8 |	Display Posts by Author
14 | Edit a Post
5 |	 Delete Posts
**28**| **Total**
