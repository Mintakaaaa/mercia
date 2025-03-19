# Recap of first three lessons

**Lesson 1: FastAPI Setup**
1. Created a new Python Replit project.
2. Installed 4 necessary dependencies.
3. Created an instance of our API.
4. Created the first API endpoint - the "root".

Questions:
1. Which dependencies did we install?
2. Which Python file creates the instance of the API?
3. What is an API endpoint?
4. Which HTTP method does the root endpoint use?

**Lesson 2: Database Integration**
1. Used CLI commands to create a database file.
2. Wrote a database population script in SQL.
3. Populated the database using the script.
4. Configured the API project to use "Models".

Questions:
1. Which command makes a new file?
2. What is a Model?
3. Which dependency makes use of Models?
4. Which Python file acts as the database-to-API configuration?

**Lesson 3: Models and Endpoints**
1. Made our first Model.
2. Created 2 API endpoints.

Questions:
1. What does the Model in our API represent?
2. What are the 2 API endpoint URLs?
3. What do the 2 API endpoints do?

# CRUD - The Backbone of Any API

---
## **What is CRUD and Why Should You Care?**

CRUD stands for **Create, Read, Update, Delete** - the four things you do in every app, whether you're making a social media site, an online store, or the next big streaming service...

Think about some commonly used apps:

1. **Instagram**
    - Create: Posting a photo.
    - Read: Scrolling through memes at 2 AM.
    - Update: Changing your bio
    - Delete: Removing old posts
2. **Amazon**
    - Create: Adding a new product
    - Read: Browsing items you'll add to your cart
    - Update: Changing product details like price or description
    - Delete: Removing discontinued products
3. **Netflix**
	- Create: ???
	- Read: ???
	- Update: ???
	- Delete: ???

Long story short: If you're building an app that deals with **data**, you need CRUD. And today, we’re making our API do just that.

---
## **CRUD in FastAPI**

FastAPI makes CRUD easy by matching them to **HTTP methods**

- `POST` → Create
- `GET` → Read
- `PUT` → Update (completely)
- `PATCH` → Update (partially)
- `DELETE` → Delete

This means:

- If you want to **add** something → Send a `POST` request.
- If you want to **see** something → Use `GET`.
- If you want to **completely change** something → Use `PUT`.
- If you want to **partially change** something → Use `PATCH`.
- If you want to **remove** something → Use `DELETE`.

Now let’s **turn theory into code**!

---
## **Writing CRUD in FastAPI**

We’ll be working with our **movie database**. First, let’s start **adding movies**!
### **(C) Create a Movie (`POST`)**

Want to add a new movie? Say no more. We’ll create a `POST` request for that because `POST` requests, unlike `GET` requests, have the special ability to add data into a database.

**Movie creation endpoint**
Add into `movies.py` under the `movies/search` endpoint the below code
```python
@app.post("/movies")
def create_movie(
	title: str, 
	genre: str, 
	director: str, 
	year: int, 
	db: Session = Depends(get_db)
):

    new_movie = Movie(
	    title = title, 
	    genre = genre, 
	    director = director, 
	    year = year
	)
	
    db.add(new_movie)
    db.commit()
    db.refresh(new_movie)
    return new_movie
```

**Explanation**

`@app.post("/movies")` 

This means that whenever someone sends a `POST` request to `/movies`, we need to run the `create_movie` endpoint. How do we know **not** to run the `read_movies` endpoint?

`db.add(new_movie)`  

This adds the new movie object into a sort of "waiting list" for the database.

`db.commit()` 

This adds everything from the "waiting list" into the database.

`db.refresh(new_movie)` 

Here, we are just making sure we get the latest info from the database (remember SQLAlchemy adds an ID to each movie). Think of this as **best practice** for now.

Test the new movie creation endpoint by entering the dev URL.
1. Click on the green `replit.dev` text to the left of the URL.
2. Click on the long green URL.
3. Add onto the end of the URL: `/docs`
4. Say "Hello" to FastAPI auto-generated documentation! *A special feature of FastAPI.*

You should see 4 different endpoints of your API. Click on the `POST` endpoint and then the `Try it out` button.

Enter the movie details:
`title = Avatar`
`genre = Sci-Fi`
`director = James Cameron`
`year = 2009`

Click `execute`... Boom! A new movie in the database.

---

### **(R) Read Movies (`GET`)**

We already learnt about this! 

Which endpoints did we make that make use of the `GET` request type?

---

### **(U) Update a Movie (`PUT`)**

Oops, we made a typo in a movie title. Let’s fix that using `PUT`. `PUT` will update a movie record **completely**, meaning **every** attribute/column of the movie record will be updated!

**Movie update endpoint**

In `movies.py`, amend the line `from fastapi import Depends` to say:
`from fastapi import Depends, HTTPException`

Add into `movies.py` under the new `POST` endpoint the below code
```python
  @app.put("/movies/{movie_id}")
  def update_movie(
      movie_id: int, 
      title: str, 
      genre: str, 
      director: str, 
      year: int, 
      db: Session = Depends(get_db)
  ):
    movie_query = db.query(Movie).filter(Movie.id == movie_id)

    if not movie_query.first():
        raise HTTPException(status_code=404, detail="Movie not found ;(")

    movie_query.update(
        {"title": title, "genre": genre, "director": director, "year": year}
    )

    db.commit()
    return movie_query.first()
```

**Explanation**

`@app.put("/movies/{movie_id}")`

Here, we specify which movie we are updating. We do this by appending a **movie id** to the endpoint.

`movie_query = db.query(Movie).filter(Movie.id == movie_id)`

We look into our database (specifically the movies table), and select the movie that matches the provided `movie_id`. Which SQL Clause does the `filter` function act as?

`if not movie_query.first()`
	`raise HTTPException(status_code=404, detail="Movie not found ;(")`

If the movie isn’t found, we send a **404: Not Found** error. There are a bunch of HTTP Status Codes. We only need to know a few of them at the moment. Have a look below...

| HTTP Status Code | Words       | Meaning                                                                                                        |
| ---------------- | ----------- | -------------------------------------------------------------------------------------------------------------- |
| 200              | OK          | Standard response for successful HTTP requests                                                                 |
| 201              | Created     | The request has been fulfilled, resulting in the creation of a new resource (like a new movie!)                |
| 400              | Bad Request | The request will not be processed because of some sort of client error, for example: malformed request syntax. |
| 404              | Not Found   | The requested resource (like a movie) could not be found.                                                      |
*(Completely optional) If you're thinking of making your own API in your free time you will probably encounter new status codes you haven't seen before; so, it would be worthwhile having a look at Wikipedia's article about HTTP Status Codes: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes*

`movie_query.update(`
	`{"title": title, "genre": genre, "director": director, "year": year}`
`)`

This updates every record that matches the movie query we specified earlier, with the new movie details.

Let's test this new endpoint! We need to re-enter the `FastAPI docs`.
You should see a new `PUT` endpoint. Enter some new movie details...

---

### **(D) Delete a Movie (`DELETE`)**

Time to erase a movie from existence. `DELETE` lets us do just that.

**Movie deletion endpoint**
Add into `movies.py` under the new `PUT` endpoint the below code
```python
@app.delete("/movies/{movie_id}") 
def delete_movie(
	movie_id: int, 
	db: Session = Depends(get_db)
):

	movie = db.query(Movie).filter(Movie.id == movie_id).first()     
	if not movie:         
		raise HTTPException(status_code=404, detail="Movie not found ;{")
	
	db.delete(movie)
	db.commit()
	return {"message": "Movie deleted successfully!"}
```

#### **Explanation**

`@app.delete("/movies/{movie_id}")`

This endpoint deletes a movie based on its ID. We should check if the movie exists before deleting (because you can’t delete what isn’t there!).

`db.delete(movie)`

This marks the specified movie for deletion.

`db.commit()`

This actually removes it from the database.

Let's test it using FastAPI docs! Delete the movie with ID = 1

---
### **(U) Update a Movie (`PATCH`)**

Another Update method? This one is slightly more complex! `PATCH` allows us to update a movie record without having to type out unnecessary columns.

**Partial movie update endpoint**
Add into `movies.py` under the new `DELETE` endpoint the below code
```python
  @app.patch("/movies/{movie_id}")
  def patch_movie(
    movie_id: int, 
    title: Optional[str] = None, 
    genre: Optional[str] = None, 
    director: Optional[str] = None, 
    year: Optional[int] = None, 
    db: Session = Depends(get_db)
  ):
    
    movie_query = db.query(Movie).filter(Movie.id == movie_id)
    movie = movie_query.first()
  
    if not movie:
      raise HTTPException(status_code=404, detail="Movie not found ;(")
  
    update_data = {}
    if title is not None:
      update_data["title"] = title
    if genre is not None:
      update_data["genre"] = genre
    if director is not None:
      update_data["director"] = director
    if year is not None:
      update_data["year"] = year

    if update_data:
      movie_query.update(update_data)
      db.commit()
      db.refresh(movie)

    return {"update_status": "success", "updated_movie": movie}
```

#### **Explanation**

`update_data = {}` 

This is a dictionary we are dynamically inserting keys and values into as per the function parameters.

`movie_query.update(update_data)`

This updates our fetched movie record, but does not yet apply the changes!

`db.refresh(movie)` 

Here we must refresh the database session to return the newly updated `movie` object.

---
## CRUD Summary

Now we have all the knowledge to interact with our database in any way we like. We can add, delete, and update movies in the database. Below is a summary of all operations we learned.

| CRUD Operation    | HTTP Method | Meaning                               |
| ----------------- | ----------- | ------------------------------------- |
| Create            | POST        | Creating a new object                 |
| Read              | GET         | Retrieving object(s)                  |
| Update (complete) | PUT         | Updating all attributes of an object  |
| Update (partial)  | PATCH       | Updating some attributes of an object |
| Delete            | DELETE      | Deleting an object                    |
