# FastAPI Authentication with JWT - Part 1

## Introduction

FastAPI with JWT Authentication

## Authentication with FastAPI and JSON Web Tokens (JWT)

Authentication is the process of verifying users before granting them access to secured resources. In this tutorial, we'll be authenticating a FastAPI application using token-based authentication, specifically JSON Web Tokens (JWT).

### Why FastAPI?

FastAPI has gained popularity due to its speed and performance advantages over other frameworks like Flask. It offers features like asynchronous requests with `async/await` syntax, making it a preferred choice for many developers.

Let's start by setting up our FastAPI project.

## Setting Up the Project

1. Create a folder for your FastAPI app:
   ```bash
   mkdir fastapi-app
   cd fastapi-app

Activate the virtual environment (assuming you're using pipenv):
```bash
pipenv shell 
```

Create a requirements.txt file with the following dependencies:
```
fastapi==0.70.0
uvicorn==0.15.0
```

Install the dependencies:

```
pipenv install -r requirements.txt
```

Create the project structure:

```
mkdir app
cd app
touch main.py
mkdir auth
touch auth/jwt_handler.py
touch model.py
```

Open the project in your preferred code editor.

## Implementing Basic Routes

Let's start by creating basic routes for testing.

main.py
``` # main.py

from fastapi import FastAPI

app = FastAPI()

@app.get("/", tags=["tests"])
def greet():
    return "Hello, world!"

```

Run the FastAPI app using the Uvicorn server:
```
uvicorn main:app --reload
```

Open the browser and navigate to http://localhost:8000 to see the greeting.

Explore the FastAPI documentation at http://localhost:8000/docs.

## Creating Post Schemas

Now, let's define schemas for our blog posts.

model.py
``` # model.py

from pydantic import BaseModel

class PostSchema(BaseModel):
    id: int = None
    title: str = None
    content: str = None

    class Config:
        schema_extra = {
            "example": {
                "title": "Some Title",
                "content": "Some Content",
            }
        }

```

In the next part, we'll cover user authentication, registration, and handling JWTs. 

## User Authentication and Authorization

User Authentication and Authorization
In this section, we'll cover the implementation of user authentication and authorization using JSON Web Tokens (JWT) in our FastAPI application.

1. #### User Sign-Up
To allow users to sign up and obtain a JWT for authentication, we have created a /signup endpoint. Users need to provide their full name, email, and password to register. The endpoint validates the input using the UserSignupSchema and returns a JWT upon successful registration.

``` # Endpoint for user sign-up
@app.post("/signup", response_model=Token)
def user_signup(user: UserSignupSchema):
    # Check if user already exists
    if check_user(user):
        raise HTTPException(status_code=400, detail="User already exists")

    # Create JWT token and return it
    return sign_jwt(user.email)
```

2. User Login
Users who have already signed up can log in using their email and password to obtain a JWT for authentication. The /login endpoint verifies the user credentials and returns a JWT upon successful login.

``` # Endpoint for user login
@app.post("/login", response_model=Token)
def user_login(user: UserLoginSchema):
    # Check if user exists and credentials are valid
    if not check_user(user):
        raise HTTPException(status_code=401, detail="Invalid login details")

    # Create JWT token and return it
    return sign_jwt(user.email)
```
3. JWT Bearer Authentication
We have implemented JWT Bearer authentication to secure our routes. The JWToken class in the auth/jwt_bearer.py file is responsible for validating the JWT passed in the Authorization header of the request. If the token is valid, the request is allowed to proceed; otherwise, an HTTP 403 Forbidden response is returned.

4. Protected Route - Posting a Blog
To illustrate the usage of JWT for protecting routes, we have added authentication to the route for posting a blog (/posts). This is achieved by adding the Depends function with the JWTBearer class as a dependency.

``` # Protected route - Posting a blog
@app.post("/posts", response_model=BlogPost, dependencies=[Depends(JWTBearer())])
def create_post(post: BlogPostCreate, current_user: User = Depends(get_current_user)):
    # Logic for creating a blog post
    # ...
    return post
```

Now, only authenticated users with a valid JWT can post a blog. Attempts to post without authentication will result in a 403 Forbidden response.



This README.md file provides an organized and step-by-step guide for setting up a FastAPI project and implementing basic routes and post schemas. The tutorial will continue in subsequent parts to cover user authentication, registration, and handling JSON Web Tokens (JWTs).
