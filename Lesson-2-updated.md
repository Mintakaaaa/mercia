# Recap of Lesson One
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

# Basic Set-up
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

4. Add to `main.py`:
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
