# web_App_Flask

A started template for Flask web apps using Python 3.8. This started template includes:

Dynamic frontend templates (not React)
Database support
Imperial LDAP user authentication
Multiple environments (development and production)
DoC Materials and Imperial Panopto API functionality

This template is useful if you would like to create a backend REST API, optionally coupled with a simple dynamic frontend.

Getting started
Once you have created your new app, take a few minutes to look through the files to familiarise yourself with the project structure.


app.py : entry point to the Flask app

templates/ : contains the frontend dynamic HTML files

static/ : contains the static frontend assets (images, stylesheets and scripts)

blueprints/ : contains all the application routes

models/ : contains all the database models/schemas

database/ : contains the database creation

config/ : contains the app settings for the different environments

The first change you should make is to set the APP_NAME variable in config/config.py to whatever you app's name is.
To start the application locally, you can just run python3 app.py and this will launch the app on port 5000 (by default).
You will notice a message in the console saying:
WARNING: Could not connect to the given database URL!
To fix this, you should set the environment variable DATABASE_URL accordingly. If you have PostgreSQL running locally, you can use that. Alternatively, you could use SQLite which is much simpler and does not require installation, for example, by running export DATABASE_URL="sqlite:///dev.db"
If you do not want to use a database yet, you can ignore this warning and delete any routes that interact with the database.
If you navigate to http://localhost:5000, you will see the response created by the route defined in blueprints/home.py.
You will also notice the lines Environment: production and Debug mode: off when the Flask application starts in the console. To enable debug mode, you must set the environment variable ENV to dev, ie: export ENV=dev (see config/config.py for more details on different environments).

Tutorial 1: Adding a new route
In this template, we have provided several routes in the blueprints/ directory. For example, we have provided all authentication-related routes (such as login and logout) in the auth.py. It is good practice to group your routes logically and create new blueprints if necessary.
To create a new blueprint, create a new file with the name of your blueprint and add:

from flask import Blueprint

my_new_blueprint = Blueprint("new_bp", __name__, url_prefix="/new_bp")


You can then add a new route to that blueprint as follows:

@my_new_blueprint.route("/hello")
def hello():
    return "Hello!"


Finally and importantly, you must register the blueprint with the Flask application. To do this, go to app.py and register the blueprint in the same style the existing code does so.
Your new route will now be accessible at /new_bp/hello.

Tutorial 2: Adding database interaction
An important part of web app development is adding database support. If you ticked the "Include DB" option when creating the app, you will have automatically received a PostgreSQL database. You can get the connection string on the dashboard and connecting to the database using it to verify that you do indeed have a database.
For local development, you should either use an SQLite db or install PostgreSQL, and set the DATABASE_URL environment variable as explained in the Getting Started section.

Defining database schemas
When working with relational databases, you must define schemas (models) for your tables. For example, if you are developing a web app where users can write blog posts, you may want to define a schema for a blog post and a user.
To do this, you can create new schemas/models in the models/ directory and create a new file. In this template, we have an Entity model which has a primary key ID, a name which is a string and an age which is an int.
For the blog example, let's create a schema for a blog post. Create a file called post.py and add:

from database.db import db

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(120), unique=False, nullable=False)
    text = db.Column(db.Test, unique=False, nullable=False)


You then need to make sure the database will create the relevant tables in the database. Go to app.py and import your model here:

# Import all your database models just like below:
with app.app_context():
    from models.entity import Entity
    if app.config["SQLALCHEMY_DATABASE_URI"]:
        db.create_all()


For more on schemas using SQL-Alchemy, read here.

Querying the database
You can now use your database to create, read, update or delete records using the SQL-Alchemy package. For example, if we want to create a new Post and add it to the database, we can do something like:

from database.db import db

post = db.session.query(Post).filter_by(title="My article").first()

new_post = Post(title="A new article!", text="This is my amazing article, I hope you enjoyed it.")
db.session.add(new_post)
db.session.commit()


For more examples, please read around the SQL-Alchemy package.

Running migrations and updating schemas
To migrate your production database, you should connect to it directly using the connection string available in the PaaS dashboard with a tool like PgAdmin (or connecting to it using your terminal), and run the relevant queries there.

Tutorial 3: Configuring a test environment
Often, we want to test that our web application is working as expected by writing a series of tests. For a simple example, you can view the code in the tests/ directory.
Unit testing a web app can be difficult, so we can test simple behaviours such as checking that a particular route is working as expected. We can setup a dummy app using a test configuration, as demonstrated in the test_home.py file. You can add more tests in this directory by making new functions with the test_ prefix.
To run these tests, you can run python -m pytest tests from the root directory. You should always run these tests before pushing/deploying new code, as they can catch out bugs. In fact, the CI/CD pipeline will run the tests for you, meaning that if a test fails, the pipeline will stop and your code won't be deployed until you fix the code causing the tests to fail.
