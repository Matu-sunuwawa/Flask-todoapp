# 🚀Flask-todoapp
In this article, we will learn how to:
- Install and Setup Flask
- Define routes
- Use templates
- Use a Database (we use SQLAlchemy and SQLite Database)
- Build TODO App functionality
- Add styling with Semantic UI

## Setup
```
mkdir myproject
cd myproject
python3 -m venv venv
```
```
source venv/bin/activate
```
or
```
. venv/bin/activate
```
Install Flask and Flask-SQLAlchemy which is used for our database.
```
pip install Flask
pip install Flask-SQLAlchemy
```
Create the Hello World app
- Create the app.py file and insert:
```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == "__main__":
    app.run(debug=True)
```
## Run the app
```
python app.py
```
- Now we can go to <code>http://127.0.0.1:5000/</code> and inspect our first running app!
Add the database
```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# /// = relative path, //// = absolute path
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///db.sqlite'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)


class Todo(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100))
    complete = db.Column(db.Boolean)


@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == "__main__":
    db.create_all()
    app.run(debug=True)
```
Change the home page to list all the todos
For this we <code>import render_template</code> and render a proper html instead of just a string. We will create the template file in the next step. Replace the <code>hello_world</code> function with this:
```
from flask import Flask, render_template

...

@app.route("/")
def home():
    todo_list = Todo.query.all()
    return render_template("base.html", todo_list=todo_list)
```

## Create the template file
Create a folder named templates in the root folder of your project. It has to be this name because Flask is looking for this directory! Now create the base.html file in this templates folder and insert:
```
!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo App</title>
</head>

<body>

    <h1>To Do App</h1>

    {% for todo in todo_list %}

    <p>{{todo.id }} | {{ todo.title }}</p>

    {% if todo.complete == False %}
    <span>Not Complete</span>
    {% else %}
    <span>Completed</span>
    {% endif %}

    {% endfor %}

</body>

</html>
```
## Add routes to add, delete, and update new todos
```
from flask import Flask, render_template, request, redirect, url_for

@app.route("/add", methods=["POST"])
def add():
    title = request.form.get("title")
    new_todo = Todo(title=title, complete=False)
    db.session.add(new_todo)
    db.session.commit()
    return redirect(url_for("home"))

@app.route("/update/<int:todo_id>")
def update(todo_id):
    todo = Todo.query.filter_by(id=todo_id).first()
    todo.complete = not todo.complete
    db.session.commit()
    return redirect(url_for("home"))

@app.route("/delete/<int:todo_id>")
def delete(todo_id):
    todo = Todo.query.filter_by(id=todo_id).first()
    db.session.delete(todo)
    db.session.commit()
    return redirect(url_for("home"))
```
## add this to our <code>base.html</code>:
```
!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo App</title>
</head>

<body>

    <h1>To Do App</h1>

    <form action="/add" method="post">
        <div>
            <label>Todo Title</label>
            <input type="text" name="title" placeholder="Enter Todo...">
            <br>
            <button type="submit">Add</button>
        </div>
    </form>

    <hr>

    {% for todo in todo_list %}

    <p>{{todo.id }} | {{ todo.title }}</p>

    {% if todo.complete == False %}
    <span>Not Complete</span>
    {% else %}
    <span>Completed</span>
    {% endif %}

    <a href="/update/{{ todo.id }}">Update</a>
    <a href="/delete/{{ todo.id }}">Delete</a>

    {% endfor %}
</body>

</html>
```
## Add styling with Semantic UI
- Put this into the head section:
```
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/semantic-ui@2.4.2/dist/semantic.min.css">
<script src="https://cdn.jsdelivr.net/npm/semantic-ui@2.4.2/dist/semantic.min.js"></script>
```
Now we just have to add some class names to add the styling. The final html looks like this:
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo App</title>

    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/semantic-ui@2.4.2/dist/semantic.min.css">
    <script src="https://cdn.jsdelivr.net/npm/semantic-ui@2.4.2/dist/semantic.min.js"></script>
</head>

<body>
    <div style="margin-top: 50px;" class="ui container">
        <h1 class="ui center aligned header">To Do App</h1>

        <form class="ui form" action="/add" method="post">
            <div class="field">
                <label>Todo Title</label>
                <input type="text" name="title" placeholder="Enter Todo..."><br>
            </div>
            <button class="ui blue button" type="submit">Add</button>
        </form>

        <hr>

        {% for todo in todo_list %}
        <div class="ui segment">
            <p class="ui big header">{{todo.id }} | {{ todo.title }}</p>

            {% if todo.complete == False %}
            <span class="ui gray label">Not Complete</span>
            {% else %}
            <span class="ui green label">Completed</span>
            {% endif %}

            <a class="ui blue button" href="/update/{{ todo.id }}">Update</a>
            <a class="ui red button" href="/delete/{{ todo.id }}">Delete</a>
        </div>
        {% endfor %}
    </div>
</body>

</html>
```
## 🚀Now if we reload the site, our app should look much nicer! Congratulations again!







