# Recap of all lessons

**Lesson 1: FastAPI Setup** (Installed dependencies, made API instance & first endpoint)
**Lesson 2: Database Integration** (Made a populated DB and wrote API-DB configurations)
**Lesson 3: Models and Endpoints** (Made our first Model and more endpoints)
**Lessons 4 & 5: CRUD**
1. Intro to CRUD and why/where it's used.
2. Making CRUD endpoints.

**Questions:**
1. What endpoints do we have in our API?
2. What model do we have in our API and what fields does it have?
3. What does CRUD stand for?
4. What special thing makes the letter U different from all the others?

Fill in the table below to relate CRUD to FastAPI!

| CRUD Operation | HTTP Method | FastAPI decorator |
| -------------- | ----------- | ----------------- |
| Create         | POST        | @app.post("...")  |
| R              | G           |                   |
| (complete) U   | U           |                   |
| (partial) U    | U           |                   |
| D              | D           |                   |

*Hint for the table above: look at your API code!*

---
# API Coding Challenges

## Challenge 1: Add a "Rating" Column

Your task is to extend the functionality of your movie API by allowing users to see and work with a movie’s **rating**.
### Task Breakdown
1. Add a new `rating` (out of 10) column to your existing database.
2. Modify your `model` and API so it handles this new column.
3. Make sure that creating and updating movies now uses the `rating`.

---
### Hints💡
- Start by editing the `populate.sql` script (you will need to delete the movies.db file to re-populate!)
- What data type would a `rating` out of 10 be?
- You should make sure the DB table has the new column. Try command: `sqlite3 movies.db` in the shell.
- You’ll also need to update your `Movie` model to match the updated table, but first import the correct **data type**.
- You need to include the `rating` in your `POST`, `PUT`, and `PATCH` requests. Test using the FastAPI auto-generated `/docs`!

---
### Completion Checklist
Tick each one as you go:

- [ ] I added a new `rating` column to the `movies` table.
- [ ] I updated the `movies` records to include the `rating`.
- [ ] I updated the `Movie` model to include `rating`.
- [ ] I updated the `POST`, `PUT`, and `PATCH` endpoints to accept and save ratings.
- [ ] I tested it all in `/docs` and it works!

---
## Challenge 2: Add Sorting by Year to the Search Endpoint

Your task is to **upgrade your `/movies/search` endpoint** so that users can choose to sort the results by movie release year.
### Task Breakdown
1. Modify your existing search route to accept a new optional query parameter, `sort_by`.
2. Allow users to pass `sort_by=year_asc` and `sort_by=year_desc` to sort movies by ascending/descending release date.

---
### Hints💡
- SQLAlchemy is packed with features! You will need to add: `from sqlalchemy import asc, desc` to implement ascending/descending sorting. Add to which file though?
- Should the new parameter `sort_by` be optional?
- Use `.order_by()`  with `asc`/`desc` on your query object, use this example:
  `query = query.order_by(asc(User.age))` 
**Obviously we don't have a User model so you need to adapt this code!**

---
### Completion Checklist
Tick each one as you go:

- [ ] I added a new SQLAlchemy import.
- [ ] I added a `sort_by` query parameter.
- [ ] If the user passes `sort_by=year_asc`, the results are sorted by year (ascending).
- [ ] I also let users sort in descending order by using `sort_by=year_desc`.
- [ ] I tested the endpoint in `/docs` and saw the sorted results.

---
## Challenge 3: Add a “Random Movie” Endpoint

Your task is to add a new route to your API that returns a **random movie** from your database. Think of it like a surprise pick when you don’t know what to watch!

---
### Task Breakdown
1. Create a new `GET` endpoint called `/movies/random`.
2. Use Python to grab all movies from the database.
3. Pick and return a random one.

---
### Hints💡
- You already have code that returns **all** movies!
- Use the `random` module to select one from the list: `random.choice(movies)`
- What should your endpoint return if the movie list is empty?

---
### Completion Checklist
Tick each one as you go:

- [ ] I imported Python’s `random` module.
- [ ] I created a `/movies/random` GET endpoint.
- [ ] I used `.all()` to fetch all movie records.
- [ ] I used `random.choice()` to return one movie.
- [ ] I handled the case where the database has no movies.
- [ ] I tested the endpoint in `/docs` and it works!

---
## Challenge 4: Add a “Watched” Feature

For the sake of this task, let’s say our movie API tracks whether **you** have watched a movie or not. To do this, we need to add a new field called `watched` and give you a way to **toggle** it on and off!

---
### Task Breakdown
1. Add a new *mandatory* `watched` column to the movies table.
2. Update the `Movie` model.
3. Update the `POST`, `PUT`, and `PATCH` endpoints to handle this new field.
4. Create a new route `/movies/{movie_id}/toggle-watched` that switches the watched status (true/false).

---
### Hints💡
- This task is basically the same as the `rating` column task but has an extra step (new endpoint).
- You will once again have to edit the `populate.sql` script.
- Don’t forget to check if the movie exists before toggling!
- You’ll use the `PATCH` method to update just *one* field.
- After updating all relevant movie fields we must call `db.refresh(movie)` before returning updated movie details at the end of the endpoint. 

---
### Completion Checklist
Tick each one as you go:

- [ ] I added a *mandatory* `watched` column to the database and existing records.
- [ ] I updated the `Movie` model to include `watched`.
- [ ] I updated the `POST`, `PUT`, and `PATCH` endpoints to support `watched`.
- [ ] I created a new `/movies/{movie_id}/toggle-watched` PATCH endpoint.
- [ ] I tested the "toggle watched" endpoint `/docs`, and it all works!

---
# Conclusion: You Just Built a Fully Functional API!

If you’ve completed these challenges, congratulations! You’ve gone from setting up a basic API to creating a fully functional movie service that can:

- Store and display detailed movie data,
- Handle real-world data fields like `rating` and `watched`,
- Support searching and sorting,
- Let users interact with data through `GET`, `POST`, `PUT`, `PATCH`, and `DELETE` methods,
- Even return a **random movie** with one click!

You’ve implemented all the major CRUD operations (Create, Read, Update, Delete), plus added cool little features like sorting and boolean toggles. These are **exactly** the kinds of skills used in **real web applications** - from Netflix, to IMDB, to any app that works with data.

---
## Next Steps - Some Ideas

- Track how many times a movie has been watched.
- Add categories or tags to movies.
- Build a simple HTML/JS frontend to interact with your API visually (have a look on Google to find out how to link HTML and JS to FastAPI and more importantly how to write HTML and JS!).

I hope you enjoyed this tiny introduction to coding APIs and that I gave you just a little flavour of web development!