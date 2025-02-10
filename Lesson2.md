# Recap of Lesson One
1. What is an API and why should we use them? Below will answer this.
2. Stripe's services provide easy handling of payment transactions.
3. Stripe provides an API for handling user payments. It stops you from having to create a payment system that communicates with banks, supports multiple payment methods and multiple card types, scheduled monthly payments, etc. Stripe does this for you and you can use their API for this, in a "plug-and-play" type of way. 
4. APIs are used everywhere, payment systems, weather apps, authentication (login, register, etc)..
5. We will be making an API that will allow users to view a movie database and search for movies.
# Programming start
6. Make a project using the Python template on Replit
7. Go to System Dependencies (imports tab); add `fastapi 0.115.6`, `sqlalchemy 2.0.36`, `uvicorn 0.32.1`
8. Go to System Dependencies (advanced tab); under System Dependencies, add `sqlite 3.45.3`
9. Add to `main.py`:
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"greeting": "Edit me!"}
```
10. Click "Run" (note nothing happens - we aren't serving the API with a webserver (uvicorn)...)
11. In `.replit`, add to bottom of file:
```toml
# link internal and external ports, and expose app to web, allow serving an api
[[ports]]
localPort = 8000
externalPort = 80
exposeLocalhost = true
```
12. In Shell, run `uvicorn main:app` (should open Webview)
13. Students: change the greeting (Edit me!) to whatever text they like...
14. (In Shell) Stop (using `Ctrl + C`) and rerun the app (using the command: `uvicorn main:app`)
# Create and populate a DB file

15. Make `populate.sql` script in base directory:
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
16. Run `sqlite3 movies.db < populate.sql` in shell
17. Check if  `movies.db`  is populated (should have nil everywhere and data inside (show example to students))
# Link the DB file to the API

18. Make `database.py` and include:
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
19. Make `models.py` and include:
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
20. Make `movies.py` and include:
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
21. Run the app and type into URL (after forward-slash): `movies` and click Enter.
22. Notice `detail: not found` text... We didn't register the endpoints to the API!
23. Add to main.py imports: `import movies`
24. Add to end of main.py: `movies.register_movie_routes(app)`
25. Run the app and try entering `/movies` endpoint, you should see all movies.
26. Explain what happened and why we can view the database of movies; go through all relevant files line by line.
# Add movie-search endpoint

27. Add to `movies.py` the code: `from typing import Optional`
28. Add to `movies.py` the endpoint code:
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
29. Test the new endpoint.
30. At this point, the directory should look like this:
```
project/ 
├── database.py
├── main.py 
├── models.py
├── movies.db 
├── movies.py
└── populate.sql
``` 
# Making a front-end

31. Create `static` folder
32. Inside `static`, create `index.html`, `script.js`, `styles.css`
33. Probably won't get this far in the second lesson...
