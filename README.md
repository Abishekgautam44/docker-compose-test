
# Try Docker Compose

This tutorial is designed to introduce the key concepts of Docker Compose whilst building a simple Python web application. The application uses the Flask framework and maintains a hit counter in Redis.



## Demo

Step 1: Define the application dependencies

    Create a directory for the project:

    $ mkdir composetest
    $ cd composetest

Create a file called app.py in your project directory and paste the following code in:

    import time

    import redis
    from flask import Flask

    app = Flask(__name__)
    cache = redis.Redis(host='redis', port=6379)

    def get_hit_count():
        retries = 5
        while True:
            try:
                return cache.incr('hits')
            except redis.exceptions.ConnectionError as exc:
                if retries == 0:
                    raise exc
                retries -= 1
                time.sleep(0.5)

    @app.route('/')
    def hello():
        count = get_hit_count()
        return 'Hello World! I have been seen {} times.\n'.format(count)

Create another file called requirements.txt in your project directory and paste the following code in:

    flask
    redis

Step 2: Create a Dockerfile

In your project directory, create a file named Dockerfile and paste the following code in:

    # syntax=docker/dockerfile:1
    FROM python:3.7-alpine
    WORKDIR /code
    ENV FLASK_APP=app.py
    ENV FLASK_RUN_HOST=0.0.0.0
    RUN apk add --no-cache gcc musl-dev linux-headers
    COPY requirements.txt requirements.txt
    RUN pip install -r requirements.txt
    EXPOSE 5000
    COPY . .
    CMD ["flask", "run"]


Step 3: Define services in a Compose file

Create a file called compose.yaml in your project directory and paste the following:

    services:
      web:
        build: .
        ports:
        - "8000:5000"
      redis:
        image: "redis:alpine"

Step 4: Build and run your app with Compose

From your project directory, start up your application by running docker compose up.

    $ docker compose up

    Creating network "composetest_default" with the default driver
    Creating composetest_web_1 ...
    Creating composetest_redis_1 ...
    Creating composetest_web_1
    Creating composetest_redis_1 ... done
    Attaching to composetest_web_1, composetest_redis_1
   

Step 5: Edit the Compose file to add a bind mount

Edit the compose.yaml file in your project directory to add a bind mount for the web service:

    services:
      web:
        build: .
        ports:
        - "8000:5000"
        volumes:
        - .:/code
        environment:
        FLASK_DEBUG: "true"
      redis:
        image: "redis:alpine"


Step 6: Re-build and run the app with Compose

From your project directory, type docker compose up to build the app with the updated Compose file, and run it.

    $ docker compose up

Step 7: Update the application

Change the greeting in app.py and save it. For example, change the Hello World! message to Hello from Docker!:

    return 'Hello from Docker! I have been seen {} times.\n'.format(count)

Refresh the app in your browser. The greeting should be updated, and the counter should still be incrementing.
hello world in browser

## Features

- Light/dark mode toggle
- Live previews
- Fullscreen mode
- Cross platform


## License

[MIT](https://choosealicense.com/licenses/mit/)


## Reference 

This project is taken from:

- Docker-docs

