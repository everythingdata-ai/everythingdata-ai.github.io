---
layout: post
title: Developing AI Applications with Python and Flask
categories: [Pyhton]
---

Today I did a mini course intended to apply Python skills to develop applications, which can be Artificial Intelligence (AI) enabled by calling external APIs (Watson, OpenAI...). 
The course can be found [here.](https://www.coursera.org/learn/python-project-for-ai-application-development)

### Main features
The main features of Flask include the following :
-  a built-in web server that runs applications in development mode. 
-  a debugger to help debug applications. The debugger shows interactive traceback and stack trace in the browser. 
- standard Python logging for application logs, and you can use the same logger to log custom messages about your application. Flask provides a way to test different parts of your application. 
- a built-in testing feature enables developers to follow a test-driven approach. 
-  access the request and response objects to pull arguments and customize responses

Some additional features of Flask :
- static assets like CSS files, JavaScript files, and images. Flask provides tags to load static files in the templates. 
- dynamic pages using Jinja templating framework. These dynamic pages can display information that may change for each request or may check if the user is logged in. 
- routing and dynamic URLs :  extremely useful for RESTful services. You can create routes for different HTTP methods and provide redirection in your application. 
- global error handlers in Flask that work on the application level. 
- supports user session management.

### Popular extensions
- Flask-SQLAlchemy add support for ORM called SQLAlchemy to Flask, giving developers a way to work with database objects in Python. 
- Flask-Mail provides the ability to set up an SMTP mail server. Flask-Admin lets you add admin interfaces to Flask applications easily. 
- Flask-Uploads allow you to add customized file uploading to your application. Here are some other extensions. 
- Flask-CORS allows your application to handle Cross-Origin Resource Sharing, making cross-origin JavaScript requests possible. 
- Flask-Migrate adds database migrations to SQLAlchemy ORM. 
- Flask-User adds user authentication, authorization, and other user management activities. 
- Marshmallow adds extensive object serialization and deserialization support to your code. `
- Celery is a powerful task queue that can be used for simple background tasks and complex multi-storage programs and schedules. 

### Built-in dependencies

Flask comes with some built-in dependencies that enable the various features : 
- Werkzeug implements WSGI or the web server gateway interface. This is the standard Python interface between applications and servers. 
- Jinja is a template language that renders the pages in your application. 
- MarkupSafe comes with Jinja. It escapes untrusted input when rendering templates to avoid injection attacks. 
- ItsDangerous is used to assign data securely. This helps determine if data has been tampered with and is used to protect Flask session cookie. 
- Click is a framework for writing command-line applications. It provides the Flask command and allows adding custom management commands.

### Flask vs Django


| Flask                                               | Django                                                          |
| --------------------------------------------------- | --------------------------------------------------------------- |
| Minimal lightweight framework                       | Full-stack framework                                            |
| Includes basic dependencies and is extensible       | Includes everything you need to create a full-stack application |
| Flexible and lets the developer take most decisions | Opinionated and makes most decisions                            |

### Basic applications and routes

Create the main server file (app.py) :

```python
from flask import Flask
app = Flask(__name__)
```

Add the first route : you want to return a message to the client when they call your server without adding a path. You need to use the @app decorator to define a route. The decorator takes the path as an argument :

```python
@app.route('/')
def hello_world():
	return "<b>My first Flask application !</b>"
```

The next step is to run your application. The first step is to create a few system environment variables. You need a variable FLASK_APP containing the name of the main server file.

```bash
export FLASK_APP=app.py
flask run
```

You can also pass the variables as arguments
```bash
flask --app app run
```

You can use jsonify to return a json file :

```python
@app.route('/json')
def index():
	return jsonify(message ='Hello, Flask!')
```

### Application configuration 

Flask provides various configuration options that you might use in your application, a part from FLASK_APP : 

- ENV – Indicates the environment, production or development, the app is running in. 
- DEBUG – enables the debug mode. 
- TESTING – enables the testing mode. 
- SECRET_KEY – used to sign the session cookie. 
- SESSION_COOKIE_NAME – the name of the session cookie. 
- SERVER_NAME – binds the host and port, and 
- JSONIFY – defaults to 'application/JSON.'

In addition, there are other ways you can provide configuration options to Flask :
- Flask provides a config object. You can insert configuration options into this object :
app.config['SECRET_KEY'] = "random-secret-key"

- If you already have environment variables, you can load them into the config object :
app.config["VARIABLE_NAME"]
app.config.from_prefixed_env()

- you can keep the configuration options in a separate JSON file and load them using the "from_file" method provided by the config object :
app.config.from_file("pathtoconfigfile")

### Application structure

As your app grows, you should create a directory structure instead of using a single Python file. There are many ways to structure your application. Here is one example: 
- store the main source code in its module directory 
- store all configurations in its file. 
- store all static assets like image, JavaScript, and CSS files separately. 
- store all dynamic content in a template directory. 
- place all test files in a test directory
- have a virtual environment that can be activated to install the right version of dependencies.

```
├── config.json
├── requirements.txt
├── setup.py
├── src
│   ├── __init__.py
│   ├── static
│	│   ├── css
│	│	│   └── main.css
│	│   ├── img
│	│	│   └── header.png
│	│   ├── js
│	│	│   └── site.js
│   ├── templates
│	│   ├── about.html
│	│   └── index.html
├── tests
│   ├── test_auth.py
│	└── test_site.py
└── venv
```

### Request and Response objects - GET and POST methods

You define the path using the route decorator. The @app.route decorator defaults to the GET method. You can however specify which method to use, sometimes both GET and POST :

```python
@app.route('/health', methods=['GET', 'POST'])
def health():
	if request.method == 'GET':
		return jsonify(status='OK', method='GET'), 200
	
	if request.method == 'POST':
		return jsonify(status='OK', method='POST'), 200
```

All HTTP calls to Flask contain the request object created from the Flask.Request class. When a client requests a resource from the Flask server, it is handled by the @app.route decorator. You can examine and explore the request object in the same method. 
Some common request attributes are :
- the address of the server in the form of a tuple, host, port. 
- the headers sent with the request. 
- the URL that is the resource asked by request. 
- access_route that lists all the IP addresses for requests that are forwarded multiple times. 
- full_path that represents the complete path of the request, including any query string. 
- is_secure is true if a client makes a request using HTTPS or WSS protocol. 
- is_JSON is true if the request contains JSON data
- Cookies dictionary contains any cookies sent with the request.

 In addition, you can access the following data from the header: 
 - Cache-Control: holds information on how to cache in browsers. 
 - Accept: informs the browser what kind of content type is understood by the client . 
 - Accept-Encoding: indicates the code content. 
 - User-Agent: identifies the client, application, operating system, or version. 
 - Accept-Language: requests for a specific language and locale. 
 - Host: specifies the host and port number of the requested server. 
 
 You can replace the request object with a custom request object, which is usually optional as the Flask Request class attributes and methods are enough.


### Getting data from Request Object

There are multiple ways to get information from the Request Object :
- Use the get_data to access data from the POST request as bytes. You are then responsible for parsing this data. 
- You can also use the get_JSON() method to get data parsed as JSON from the post request. 

Flask also provides more focused methods to get exact information from the request, so you don't have to parse the data into a specific type :
- args will give you query parameters as a dictionary. 
- JSON will parse the data into a dictionary. 
- files will provide you user uploaded files. 
- form contains all values posted in a form submission.
- values combine the args data with the form data

### Response object 

When a client calls a URL, it expects a response back. Flask has built-in response class you can leverage. Some common response attributes include the following: 
- status_code indicates the success or failure of the request. 
- headers give more information about the response. 
- content_type shows the media type of the requested resource. 
- content_length is the size of the response message body. 
- content_encoding indicates any encoding applied to the response, so the client knows how to decode the data. 
- mime-type sets the media type of the response. 
- expires contains the date or time after which the response is considered expired.

A Response object with a status_code of 200 and a mime-type of HTML is automatically created for you when you return data from the @route method. 
JSONify also creates a Response object automatically. 
You can use make_response to create a custom response. 
Flask provides a special redirect method to return a 302 status-code and redirect the client to another URL. 
Finally, Flask provides an abort method to return a response with an error condition.

### Dynamic routes 

Let's look at an example of how you might call an external API in Flask. The easiest way is to use the Python requests library. You can return the JSON from the external API to your client. However, you can also process the results before sending them back to your client. Here is an example :

```python
@app.route('/')
def get_author():
	res = requests.get('https://openlibrary.org/search/authors.json?q=j%20k%20rowling')
	
	if res.status_code == 200:
		return {"message": res.json()}
	
	elif res.status_code == 404:
		return {"message": "Not Found"}, 404
	
	else :
		return {"message": "Unknown error"}, 500
```

When developing RESTful APIs, you can send some resource-id as part of the request URL. For example, you want to create an endpoint that returns book information by International Standard Book Number (ISBN), but instead of hard coding the ISBN, you want the client to send it as part of the URL Flask provides dynamic routing for this purpose Let's look at a concrete example :

```python
@app.route('/book/<int:isbn>')
def get_author(isbn):
	res = requests.get(f"https://openlibrary.org/api/volumes/brief/isbn/{escape(isbn)}.json")
	
	if res.status_code == 200:
		return {"message": res.json()}
	
	elif res.status_code == 404:
		return {"message": "Not Found"}, 404
	
	else:
		return {"message": "Unknown error"}, 500
```

You can define the type of the variable to validate the request :
@app.route('/terminals/<string:airport_code>')


### Rendering templates 

```python
from flask import Flask, render_template, request


app = Flask("My first application")

  

@app.route('/sample')
def getSampleHtml():
	return render_template('index.html')

@app.route('/user/<username>', methods=['GET'])
def getUser(username):
	return render_template('result.html', username=username)

@app.route('/user', methods=['GET'])
def greetUserBasedOnReq():
	username = request.args.get('username')
	return render_template ('result.html', username=username)

if __name__ == '__main__':

app.run(debug=True)
```

### Decorators in Flask

- Method decorators : Let’s say we have a python code where we want all the output to be in JSON format. It doesn’t make sense to include code for these in each of the methods as it makes the lines of code redundant. In such cases, we can handle this with a decorator.

```python
def jsonify_decorator(function):
    def modifyOutput():
        return {"output":function()}
    return modifyOutput

@jsonify_decorator
def hello():
    return 'hello world'

@jsonify_decorator
def add():
    num1 = input("Enter a number - ")
    num2 = input("Enter another number - ")
    return int(num1)+int(num2)

print(hello())
print(add())
```

The method decorator is also referred to as the wrapper, which wraps the output of the function, that it decorates. In the above code sample, `jsonify-decorator` is the decorator method. We have added this decorator to `hello()` and `add()`. The output of these method calls will now be wrapped and decorated with the jsonify_decorator.


- Route decorators : 
You may have observed when you visit any domain you have the root and then have other end-points you can access : 
`https://mydomain.com/`
`https://mydomain.com/about`
`https://mydomain.com/register`

 to define these endpoints in Python we use what we call Route Decorators : 
 
 ```python
 @app.route('/')
 def home():
	 return "Hello World!"
```

@app.route(“/“) is a Python decorator that Flask provides to assign URLs in our app to functions easily. You can easily tell that the decorator is telling our @app that whenever a user visits our application’s domain, in our case, execute the `home()` function.

We can handle multiple routes with a single function by just stacking additional route decorators above the method which should be invoked when the route is called. The following is a valid example of serving the same “Hello World!” message for 3 separate routes:

```python
@app.route("/")
@app.route("/home")
@app.route("/index")
def home():
    return "Hello World!"
```

The route decorator can also be more specific. For example, to get the details of a user whose userId is U0001, you may go to  
`http://mydomain.com/userdetails/U0001`. It doesn’t make sense to define a different route for each user you may be dealing with. In such cases, we define the route like this.

```python
@app.route("/userdetails/<userid>")
def getUserDetails(userid):
    return "User Details for  " + userid
```
