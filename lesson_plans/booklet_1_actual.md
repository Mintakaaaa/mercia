***MADE IN COLLABORATION WITH MINDAUGAS BRAZLAUSKAS & MR JENNINGS***
# Recap of APIs
## What is an API?
An API (Application Programming Interface) is like a middleman that lets different software apps or websites communicate with each other. Instead of manually handling tasks, an API allows programs to send requests and receive responses instantly.

## Stripe and their API...
Stripe is an online payment processing service that businesses use to accept payments. Instead of building an entire payment system from scratch, software developers can use Stripe’s API to process payments, handle refunds, and manage subscriptions.

## Making a Payment with Stripe's API
When a customer buys something online, the process might look like this:

1. Your website/app collects the user's payment details.
2. It sends a request to the Stripe API (to charge the customer’s card).
3. Stripe's API processes the payment securely.
4. The API responds with a confirmation that the payment was successful (or failed).
5. Your website displays a success/failure message to the customer.

You can even start using Stripe's API with Python!

## Some notes...
1. APIs are used everywhere: payment systems, weather apps, authentication (login, register, etc)..
2. We will be making an API that will allow users to view a movie database and search for movies.

# Basic API Set-up
1. If you haven't already done so, create a new Python application on Replit.
2. Go to `System Dependencies` (imports tab); add `fastapi 0.115.6`, `sqlalchemy 2.0.36`, `uvicorn
0.32`.
3. Go to `System Dependencies` (advanced tab); under `System Dependencies`, add `sqlite 3.45.3`

These dependencies are `Python` modules that either allows us to add new features to our application, or
makes the development process more efficient.

1. `fastapi` A tool for building websites and apps that let computers talk to each other (APIs) using
Python. It’s fast, easy to use, and automatically checks if the data sent to your app is correct.
2. `uvicorn` A basic webserver that runs FastAPI apps and makes them ready to handle many users at the
same time.
3. `sqlalchemy` A Python tool that helps you work with databases more easily by letting you use Python
code instead of writing complex SQL commands. It also gives you the option to write direct SQL if
needed.
4. `sqlite` A simple, built-in database that saves data in a file on your computer. It’s good for small
projects or testing because it doesn’t need a separate server to run. We will learn more details about
each of these modules during development.

5. Add to `main.py`:
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"greeting": "Edit me!"}
```

If we try pressing run at this point, nothing happens! Not even an error! This is because we haven't setup the
webserver (`uvicorn`) to make our API available.

Let's breakdown what each line of the above code does:
```python
from fastapi import FastAPI
```

This imports the fastapi module into our code.

```python
app = FastAPI()
```
This creates a new fastapi application.

```python
@app.get("/")
```

This is an example of a Python "decorator", which modifies the function it is attached to in a particular way. In
this case we want the function to be run when we access the root page of our application. When we want to
get information from our application we use a HTTP GET request, which is why the word get appears in the
decorator.

This is our first **API endpoint**. An API endpoint is a URL that represents a resource or action in our API. An example of this is the "home page" of our api that displays a greeting.

```python
def read_root():
    return {"greeting": "Edit me!"}
```

This is the function that runs when the root page of our application receives a GET request. In this case the
function simply returns some data. In this case we return a Python dictionary consisting of a single key-value
pair. The key is `greeting` and the value is `Edit me!`. Once we have finished our setup we will come back and
modify this code.

6. In `.replit`, add to bottom of file:

```toml
# link internal and external ports, and expose app to web, allow serving an api
[[ports]]
localPort = 8000
externalPort = 80
exposeLocalhost = true
```

Multiple different programs may be receiving data over the same network connection. In order for the correct
program to receive the correct piece of data, each program chooses to listen on a particular port. Any data
that is sent to that port will be forwarded to the program.
When developing on Replit there is an extra degree of complexity. Applications on Replit run inside a sort of
virtual machine (a "sandbox"). We need to link the ports on the "virtual machine" to the external ports. For
much more detail on this idea see: docs.replit.com/replit-workspace/ports.

7. In Shell, run `uvicorn main:app`. This starts the uvicorn webserver, serving our app. You should see the
key-value pair that we defined above.

	Why `uvicorn main:app`?
	1. `uvicorn` is the web server we are using to serve our API to us.
	2. `main` is the name of the entrypoint file to our API.
	3. `app` is the variable given to the FastAPI instance.

8. Change the greeting ("Edit me!") delivered by our app to a message of your own. Even if you try
refreshing the webview, your new message will *not* appear. This is because `uvicorn` is still serving the *old*
version of our app.
9. Make sure the Shell tab is open and stop, using `Ctrl + C`, the app before re-running it with `uvicorn
main:app`.
10. Programmers LOVE automation... Instead of using `Ctrl + C` and `uvicorn main:app` to see changes to our API (which takes a few steps), let's just make a tiny change to the function of the "Run" button...
	1. Search for the `.replit` file and add: `run = uvicorn main:app --host=0.0.0.0 --port=${PORT:-8000}`
 	2. This will allow the "Run" button to tell `uvicorn` to serve our API to us.

---

***IN PRACTICE: LESSON 1 ENDED HERE***

Recap questions to ask in Lesson 2:
1. Which tool did we use the make the API instance?
2. Which tool did we use to serve the API?
3. Which HTTP method is used to read the root of the API?
4. What do the commands `ls`, `cd`, `cd ..`, `cd workspace` do?

---
# Creating a populated database file

Let's now make a database to store all of our movies.

11. Enter the Shell.
12. Run `touch populate.sql`.

Notice a new file in your root directory? What you just did was create a new file of the format `.sql`.

The `touch` command is one of many Command Line Interface (CLI) commands that we often use whilst programming.

This command simply creates a new, empty file. The syntax is: `touch <file_name_with_extension>`

13. Let's add this SQL to the `populate.sql` script.
```sql
CREATE TABLE movies (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  genre TEXT NOT NULL,
  director TEXT NOT NULL,
  year INTEGER NOT NULL
);

INSERT INTO movies (title, genre, director, year) VALUES
('Inception', 'Sci-Fi', 'Christopher Nolan', 2010),
('The Avengers', 'Action', 'Joss Whedon', 2012),
('Mean Girls', 'Comedy', 'Mark Waters', 2004),
('The Dark Knight', 'Action', 'Christopher Nolan', 2008),
('Pulp Fiction', 'Crime', 'Quentin Tarantino', 1994);
```
Some of this code should look familiar from your lessons on SQL!

```sql
CREATE TABLE movies (
id INTEGER PRIMARY KEY AUTOINCREMENT,
title TEXT NOT NULL,
genre TEXT NOT NULL,
director TEXT NOT NULL,
year INTEGER NOT NULL
);
```
This section creates a new table called `movies` with fields for `id`, `title`, `genre`, `director`, and `year`. Each field is specified
to have a datatype of either `INTEGER` or `TEXT`, they are also set to be `NOT NULL`, which means that they can't
be left blank. `id` is set as the `PRIMARY KEY`, meaning that it is a *unique* value. `id` is also given the property
`AUTOINCREMENT`, meaning that each new record in the table will be automatically assigned the next available
value as its `id`.

```sql
INSERT INTO movies (title, genre, director, year) VALUES
('Inception', 'Sci-Fi', 'Christopher Nolan', 2010 ),
('The Avengers', 'Action', 'Joss Whedon', 2012 ),
('Mean Girls', 'Comedy', 'Mark Waters', 2004 ),
('The Dark Knight', 'Action', 'Christopher Nolan', 2008 ),
('Pulp Fiction', 'Crime', 'Quentin Tarantino', 1994 );
```
This section simply inserts some movies into the table. Notice that we don't need to specify a value for `id`.


14. In the Shell, run `sqlite3 movies.db < populate.sql`.

	This command looks a little intimidating, so let's break it down:
	1. `sqlite3 movies.db`: This part opens the database file named `movies.db` using the `sqlite3` module. If the file doesn't exist, SQLite will create it.
	2. `< populate.sql`: The `<` operator redirects the content of the file `populate.sql` as input into the `sqlite3` program. Essentially, SQLite will run all the SQL commands in that file.

15. We should check if  `movies.db`  exists and is populated.

	We can do this in two ways.
	1. By double-clicking the `movies.db` file and looking for a bunch of unreadable text where a tiny portion of that text is readable.
	2. By carrying out the following commands in the Shell:
		1. `sqlite3 movies.db`
		2. `SELECT * FROM movies;`
		3. When finished: `.quit`

Why is most of the `movies.db` file unreadable? This is because SQLite databases store information in a *binary* format, which doesn't translate well to human-readable characters.

# Linking the Database File to the API

16. Using the `touch` command, create a new `database.py` file.

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "sqlite:///./movies.db"

engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```
It is not necessary for us to understand every line of this code, but we can get some basic ideas.

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
```
This section of the code imports the necessary tools from `sqlalchemy`.

```python
DATABASE_URL = "sqlite:///./movies.db"
```
This line specifies the location of the database. `./movies.db` specifies that the database file is in the same
folder as this `database.py` file.

```python
engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
```
This creates the engine, which is the *starting point* for any `sqlalchemy` application. We provide the engine
with the location of our database and set the `check_same_thread` parameter to `False`, allowing separate
threads to access the database simultaneously.

```python
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
```
This line of code creates a session. A session keeps track of any changes that we wish to make to the
database, but it doesn't make them permanent until we `.commit()` the session. The parameters
`autocommit=False` and `autoflush=False` make sure that we are deciding when to make permanent
changes to the database. This is best-practice and a common configuration. `bind=engine` simply connects the session to the engine we previously created.

```python
Base = declarative_base()
```
This line of code will allow us to define SQL tables as python classes. `Base` will be the class that our models will
inherit from. Models are things that represent objects in our system, like a user, or a movie.

```python
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```
This function returns (yields) a database session and ensures it is closed properly when we are finished using
it. This is very important because this allows us to have a single, central method of creating a database session between different API endpoints. It also protects us from fatal errors as the `finally` block closes the connection to the database before returning an error to us.

---

***IN PRACTICE: LESSON 2 ENDED HERE***

Recap questions to ask in Lesson 3:
1. Which CLI command creates a new file?
2. What database did we create?
	1. How many tables did this database have?
	2. What columns did the database table have?
3. Which CLI command did we use to populate the movies database?
4. Which new tool did we introduce into the project?
5. Which column's data do we **not** enter when making a new record? Why?
6. Why did we make the `get_db()` function, and what is its significance?

---
## Let's now make our first Model
17. Using the `touch` command, create a new file `models.py` and add the following code:

```python
from sqlalchemy import Column, Integer, String
from database import Base

class Movie(Base):
    __tablename__ = "movies"
    id = Column(Integer, primary_key=True)
    title = Column(String)
    genre = Column(String)
    director = Column(String)
    year = Column(Integer)
```

This file creates our **movie model** that matches the format of the movies table in our database. It is necessary that we make a model class that is understood by Python because we need to extract movie object out of the database records so that we can work with it. We could just completely ignore the concept of models and forget about sqlalchemy but that would open us up to a lot of errors and inefficient work!

```python
from sqlalchemy import Column, Integer, String
```
This line imports the correct datatypes for our class to use. Each field in our database table is modelled as a
`Column`, with a datatype of either `Integer` or `String` (which corresponds to `TEXT` in our SQL query).

```python
from database import Base
```
Here we import the `Base` class that we created in the previous file.

```python
class Movie(Base):
    __tablename__ = "movies"
    id = Column(Integer, primary_key=True)
    title = Column(String)
    genre = Column(String)
    director = Column(String)
    year = Column(Integer)
```
We then define our movie model class to match the structure of a movie record in the movies table in our database. Notice that the
`Movie` class inherits from `Base` to ensure that the class we've created is correctly formatted for use with
sqlalchemy.

18. Using the `touch` command, create a new python file `movies.py` and add the code:

```python
from fastapi import Depends
from sqlalchemy.orm import Session
from models import Movie
from database import get_db

def register_movie_routes(app):
    @app.get("/movies")
    def read_movies(db: Session = Depends(get_db)):
        return db.query(Movie).all()
```
This file adds a new route (API endpoint) to our application that allows us to view all the movies in our database.

```python
from fastapi import Depends
from sqlalchemy.orm import Session
from models import Movie
from database import get_db
```

This section imports all the code required for this file. Notice that we have imported the `Movie` class and
`get_db` function that we have defined previously.

```python
def register_movie_routes(app):
```
Rather than directly writing the new route into main.py as we did previously, we instead define an outer
function `def register_movie_routes(app):` whose only job is to define new routes (API endpoints) all in one go. There are several
reasons for creating new routes in this way, but a simple reason is that it stops main.py becoming cluttered
with the many different routes that a large application may have.

```python
@app.get("/movies")
```
This line is the decorator that links a particular url to following function. Students: which HTTP method does this decorator represent?

```python
def read_movies(db: Session = Depends(get_db)):
```
In the definition of the `read_movies` function we pass the parameter db, this is of type `Session` and by default is set by the
value yielded from running the `get_db` function that we defined previously. This is an **important** concept called "dependency injection" - by using such a parameter, we automatically provide a database session to our API endpoint.

```python
return db.query(Movie).all()
```
The body of the function runs a query on the movies table inside our database. As we have not provided any
restrictions, the query will return all the movies in the database. The `.all()` command says that we want to
return everything that was found by the query, instead of just the first result. Think of this as the SQL command: `SELECT * FROM movies`.

19. Run the app and type into the URL (after forward-slash): `movies` and click Enter.
20. Notice `detail: not found` text... We didn't *register* the endpoints to the API!
21. Add to top of main.py: `import movies`
22. Add to end of main.py: `movies.register_movie_routes(app)`
23. Run the app and try entering the `/movies` endpoint, you should see the list of all movies.

# Adding the movie-search Endpoint

24. Add to `movies.py` the code: `from typing import Optional`

Remember that python is a dynamically typed language, meaning that we don't need to specify datatypes for variables and parameters.
However, it can sometimes be useful to give "hints" to what datatypes we are expecting in our code.
The `typing` module allows us to add these "hints" and specifically `Optional` allows us to specify that a
parameter is either of a particular type or can be `None`.

25. Add to `movies.py` the endpoint code:

```python
@app.get("/movies/search")
def search_movies(
    genre: Optional[str] = None,
    director: Optional[str] = None,
    year: Optional[int] = None,
    db: Session = Depends(get_db),
):
    query = db.query(Movie)
    if genre:
        query = query.filter(Movie.genre == genre)
    if director:
        query = query.filter(Movie.director == director)
    if year:
         query = query.filter(Movie.year == year)
     return query.all()
```
As the name suggests, this API endpoint allows us to search for movies in our database using specific column names.

```python
@app.get("/movies/search")
def search_movies(
genre: Optional[str] = None,
director: Optional[str] = None,
year: Optional[int] = None,
db: Session = Depends(get_db),
):
```
This section of the code defines a new API endpoint, but (other than `db: Session = Depends(get_db)` which we've already discussed) this is the first time we've seen a function defining an endpoint taking parameters. `genre`, `director`, and `year` are all parameters that can be set. `Optional[str] = None` allows us to exclude a parameter from our function call (which would mean this parameter would be of type `None`).


While we will later use a HTML form to choose values for these parameters, we can do so for now through the
URL. Typing in the URL `/movies/search?year=2008` returns only those films in the database from the year 2008. 

Typing in `/movies/search?genre=Action&director=Joss%20Whedon` filters the movies down to those with genre set to Action and director set to Joss Whedon.

```python
query = db.query(Movie)
if genre:
    query = query.filter(Movie.genre == genre)
if director:
    query = query.filter(Movie.director == director)
if year:
    query = query.filter(Movie.year == year)
return query.all()
```

As we saw previously `query = db.query(Movie)` runs a query on our database, returning all the movies stored in the movies table. We then want to run a series of **filters** on this query, for example `query = query.filter(Movie.genre == genre)` filters the list of movies down to only those with `genre` field equal to the `genre` parameter. If you are confused by `if genre`, remember that `None` is a *falsy* value, meaning that if a value is not given to the `genre` parameter, then we **do not** filter by it.

At this point, the directory should look like this:

```
project/
├── database.py
├── main.py
├── models.py
├── movies.db
├── movies.py
└── populate.sql
```

---

***IN PRACTICE: LESSON 3 ENDED HERE***


Recap questions to ask in Lesson 4 are in Booklet 2 and include questions about all previous lessons (lessons 1-3).

---