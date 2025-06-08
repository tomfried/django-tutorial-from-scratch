# Django Tutorial From Scratch
What follows is my recommended guide for getting folks familar with Django-Python and learning what the application takes by building it from scratch. There is no better way to learn than by being hands on and this will check your progress along the way with screenshots of what you should be seeing both in your app and in your file structure.

-------
**Table of Contents**
- Setup Django Project
- Switch to MariaDB instead of Sqlite3 (Optional)
- Create .gitignore file
- Make Tweaks to File Structure
  - Move static files to parent directory for all and rename "assets".
  - Move all views (HTML files) to own folder in parent directory
  - Change "Hello World" to "Home" and make Controllers directory where all the backend code will live
  - Create layout and add Header, Footer, and HTML Head as Partials/Templates
- Create new Container/View (given current setup)
- Create new Controller, View, AND Model (given current setup)
- Add SCSS/SASS to Project
- Wrap Project in Docker Container


## Setup Django Project
**Example is for `MAC` devices. Use `apt-get` or `yum` in place of `brew` commands for the equivalent on `Linux`.**
1. Install Homebrew (used in place of `apt-get` and `yum` to install packages).
```/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"```
2. Install python3: `brew install python3`. Ensure above `3.7` by doing a `python --version`.
3. Install git: `brew install git`.
4. See if pip installed: `python3 -m pip --version`. If not, install it using python 3 (I think `python3 get-pip.py`).
5. Create new folder "Projects" on your Desktop.
6. Navigate to Projects folder from Desktop: `cd Projects`.
7. In projects, initialize git: `git init`.
8. Clone the git repo attached to the project to populate the home directory of the EC2 instance with said code repo. ex: `git clone https://github.com/LondonAppDeveloper/demo-django-hello-world-starter` or wherever your project exists. Verify you see a folder generate titled "demo-django-hello-world-starter".
9. Next navigate to that folder: `cd demo-django-hello-world-starter` or whatever the project folder is named.
10. Create virtual environment for the project: `python3 -m venv env`.
11. Activate virtual environment: `source env/bin/activate`.
12. Install Django (via requirements in this example: `pip install -r requirements.txt`). To do this manually do `python3 -m pip install Django`.
If you did it the manaul way, make sure the version it right for your app. Check Django version: `python3 -m django --version`. Ensure `3.0` or whatever.
13. Test running the server to ensure it's working: `python3 manage.py runserver`.
14. Verify you see as follows when navigating to `http://127.0.0.1:8000/`.
<img width="883" alt="hello world precursor to kubernetes deployment" src="https://user-images.githubusercontent.com/7783699/112783915-64d48500-901e-11eb-958e-3f62d0370f9b.png">
15. Verify your project directory looks as follows:
<img width="324" alt="project directory at start" src="https://user-images.githubusercontent.com/7783699/113534861-da50d000-959f-11eb-9c13-b9b5bc22bf0b.png">


## Switch to MariaDB instead of Sqlite3 (Optional)
1. Install it: `brew install mariadb`. On Linux do (I think) `yum install python-pip python-dev mariadb-server libmariadbclient-dev libssl-dev`.
2. Run the database installed if you don't have it already: `Run mysql_install_db`.
3. Start mariadb: `mysql.server start`.
4. Setup a password: `sudo mysql_secure_installation`.
5. Enter machine password because you used a sudo then on the first prompt don't enter a password for root (because it's a fresh install of Mariadb).
6. When asked to switch to UNIX_socket authentication, say `n` to mean no.
7. When asked to change root password, select `y` to set it to whatevet you want.
8. Then for security purposes, select `y`for the next 4 prompts. Will ask removing anonymous users, disallowing remote access, remove test database that everyone can access, and applying these changes immediately respectively.
9. Verify you can access MariaDB: `mariadb -u root -p`.
10. Create database: `CREATE DATABASE myproject CHARACTER SET UTF8;`
11. Enter database: `use myproject;`
12. Verify you can get and viewing tables is empty but does work: `view tables;`
13. Now exit mariadb with: `exit` then reactivate your environment if not already in it: `source env/bin/activate`.
14. Now install pip wheel (to help with installs). `pip install wheel`.
15. Now install mysqlclient to allow us to use the database just configured: `pip install django mysqlclient`.

IF you run into issues, install these: `sudo apt-get install python-dev python3-dev`, `sudo apt-get install libmysqlclient-dev`, `pip install pymysql`, then `pip install mysqlclient` again. NOTE to use yum instead of apt-get if you use yum. As per MAC users, instead have to install XCode, `sudo xcodebuild -license accept`, then `brew install mysql-connector-c`.

16. Enter mysql by typing `mysql`, create a new database called ex. `mydatabase` (`CREATE DATABASE myproject CHARACTER SET UTF8;`), create a new user called ex. `mydatabaseuser` (`CREATE USER mydatabaseuser@localhost IDENTIFIED BY 'password';`).
17. Next grant said `mydatabaseuser` full permission to the `mydatabase`: `GRANT ALL PRIVILEGES ON mydatabase.* TO mydatabaseuser@localhost;`.
18. Update the permissions: `FLUSH PRIVILEGES;` then exit mysql: `exit`;
19. Lastly in the `/app/app/setting.py` just need to switch: 
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```
to (WITH PROPER EDITS TO NAME, USER, PASSWORD, HOST, and PORT).
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        #'PASSWORD': '',
        #'HOST': '',
        #'PORT': '',
    }
}
```
Refer to [Django Docs - Databases[(https://docs.djangoproject.com/en/2.2/ref/settings/#databases) for assistance with this.

## Create .gitignore file
In order to push to Git without all of the `env`, `sqlite3`, or `pycache` files, need to create a `.gitignore` file. If you git cloned from the earlier mentioned link, this was already pulled in, but if you made it from scratch instead, should add one.
1. In outter project folder (NOT under `/app`) create a new file called `.gitignore`. Don't forget the period in front of the name.
2. Edit this file and paste the some ~100 lines found here: https://djangowaves.com/tips-tricks/gitignore-for-a-django-project/ in it and save.
3. Verify you see the file in command line. Note you won't see it looking at it in a Finder or Files application because the `.` makes it hidden. Doing a `ls -a` via command line will reveal files beginning with `.`.
![gitignore-found-in-command-line](https://user-images.githubusercontent.com/7783699/113875183-805d2f80-9784-11eb-977d-7b160fe5d627.png)

## Make Tweaks to File Structure
### Move static files to parent directory for all and rename "assets".
1. Move current `/static/` from within your `hello_world` directory up one level so all apps can access it.
2. Rename it `assets` and create 3 new folder underneath it: "css", "js", and "images".
3. If using the example git project from earlier, move the coconut bars image under images.
4. Go to the `settings.py` under `/app/app/settings.py` and at the very bottom where it says `STATIC_URL = '/static/'`, replace that with the following:
```
STATIC_URL = 'assets/'

STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "assets")
]
```
5. If using the example git project from earlier, go to where the coconut bars image is references under app/hello_world/templates/index.html and replace the source of the image from `src="{% static 'coconut_bars.jpg' %}"` to `src="{% static 'images/coconut_bars.jpg' %}"`. Then that's it. Going forward all assets will exist in this one `assets` folder and just need to reference `css/`, `js/`, or `image/` when adding it.
<img width="637" alt="hello world after moving assets out" src="https://user-images.githubusercontent.com/7783699/113500108-74a20c80-94e9-11eb-8509-f82a39c17380.png">

### Move all views (HTML files) to own folder in parent directory
1. If using the example git project from earlier, move the `app/hello_world/template`folder up a directory and then change the name of it to `views` so it is now shown as `app/views`. Reason for the name change is to more accurately represent the Model-View-Controller aspects of the app for newbies to more easily understand.
2. In the folder where the `index.html` file was, create a new folder called `hello_world` and then put the `index.html` file inside of it.
3. Now change reference of the `index.html` in the `/app/hello_world/views.py` file. Change `return render(request, 'index.html')` to `return render(request, 'hello_world/index.html')`.
4. Lastly to make the project knows to add this directory of views (templates) to it, go to `/app/app/settings.py` and under `TEMPLATES = [` section, where it says `'DIRS': [` replace with the following:
```
'DIRS': [
    os.path.join(BASE_DIR, 'views'),
],
```
5. Reload server and verify the home page despite being in a new directory still loads as if nothing has changed.

### Change "Hello World" to "Home" and make Controllers directory where all the backend code will live
1. Change `/app/views/hello_world` folder to `/app/views/home`.
2. Change `/app/hello_world` folder to `/app/controllers`.
3. Change `/app/settings.py` reference in the `INSTALLED_APPS = [` section of `'hello_world',` to `'controllers',`.
4. Change `/app/urls.py` reference in the `INSTALLED_APPS = [` section of `from hello_world import views` to `from controllers import views`.
5. Change the now `/app/controllers/apps.py` mention of: `return render(request, 'home/index.html')` to `return render(request, 'home/index.html')`
6. Change the now `/app/controllers/apps.py` mention of:
```
class HelloWorldConfig(AppConfig):
    name = 'hello_world'
```
to
```
class ControllersConfig(AppConfig):
    name = 'controllers'
```
7. Create new folder under the now `/app/controllers` called `home` and place the `views.py` in it.
8. Then create a new file in this `home` folder titled `__init__.py`.
9. Edit the `__init__.py` file and add the following to it: `from .views import *`. Verify this is the only thing in this file.
10. Lastly just need to go to `/app/app/urls.py` file and change the `path('', views.index)` line to `path('', home.index)`.
11. Verify restarting the server still makes the same hello world message and graphic appear correctly. If so, your file structure should look like this:
<img width="423" alt="file structure after customization" src="https://user-images.githubusercontent.com/7783699/113499996-98188780-94e8-11eb-9172-f86ce11eef8b.png">

### Create layout and add Header, Footer, and HTML Head as Partials/Templates
1. In the `/app/views` folder, create a new one titled `layout`.
2. Create a new file in there titled `base.html`.
3. Paste the following it it:
```
<!DOCTYPE html>
<html lang="en">
    {% include "layout/html_head.html" %}
    <body>
        <header>{% include "layout/header.html" %}</header>
        <main>{% block content %}{% endblock %}</main>
        <footer>{% include "layout/footer.html" %}</footer>
    </body>
<html>
```
4. In the same directory (`/app/views/layout`) create a new file titled `html_head.html` and put the just the following in it for now:
```
{% load static %}
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
    <link type="text/css" rel="stylesheet" href="{% static 'css/styles.css' %}">
    <title>My Django App</title>
</head>
```
5. Create the file `header.html` in the same location and just paste the following it for now:
```
{% load static %}
<div class="content-container">
    <a href="/">
        <img class="logo" src="{% static 'images/logo.png' %}" alt="home">
    </a>
</div>
```
6. Create the file `footer.html` in the same location and just paste the following it for now:
```
<div class="content-container">Footer works too!</div>
```
7. Download this royalty free image below (courtesy of [postermywall])(https://www.postermywall.com/index.php/art/template/2402e0689677112e3b2b6e0f399d7dc3/corporate-company-logo-design-template#.YGpe6EhKgmJ) and place it in the `/app/assets/images/` directory.

![logo](https://user-images.githubusercontent.com/7783699/113526407-f5afe100-9587-11eb-9b48-93b6b0dc42df.png)


8. Create a new file in the `/app/assets/css/` directory called `styles.css`. NOTE, instructions will be added below eventually on doing everything in SCSS instead of CSS but for now just copy/paste the following into it:
```
body {
    margin: 0;
}
header,
footer {
    background: #ddd;
}
header {
    border-bottom: 1px solid #ccc;
}
main {
    min-height: calc(100vh - 162px);
}
img.logo {
    max-width: 140px;
}
.content-container {
    margin: 0 1.5em 0 2em;
    max-width: 100%;
}
footer {
    border-top: 1px solid #ccc;
}
footer > div {
    padding: .7em 0;
}
```
9. Edit the `/app/views/home/index.html` file and make it just following to work with the new layout:
```
{% extends "../layout/base.html" %}
{% load static %}

{% block content %}
	<div id="homePage" class="content-container">
		<h1>Some delicious coconut bars</h1>
		<img src="{% static 'images/coconut_bars.jpg' %}" alt="Delicious chocolate coconut bars" />
	</div>
{% endblock %}
```
10. Run server again and verify you see as shown below:
<img width="1583" alt="hello world after adding templates" src="https://user-images.githubusercontent.com/7783699/113534564-16376580-959f-11eb-8552-ba92e47465fd.png">
<img width="438" alt="directory after adding templates" src="https://user-images.githubusercontent.com/7783699/113534660-5e568800-959f-11eb-9f87-17fff4bfe8e2.png">

## Create new Container/View (given current setup)
1. Duplicate the `/app/controllers/home/` directory then rename it ex. `admin`.
2. Open up the now `/app/controllers/admin/view.py`and replace the line: `return render(request, 'home/index.html')` with `return render(request, 'admin/index.html')`. Just remember to indent properly. To do steps 1-2 manually instead, create new `admin` folder, create a `view.py` for it and a `__init__.py` in the folder too, just add `from .views import *` to the `/app/controllers/admin/__init__.py` file and then add the following to the `/app/controllers/admin/views.py`:
```
from django.shortcuts import render


def index(request):
    """Placeholder index view"""
    return render(request, 'admin/index.html')

```
4. Duplicate the `/app/views/home/` directory then rename it to ex. `admin`. Then replace all of the `/app/controllers/admin/index.html` file with the following:
```
{% extends "../layout/base.html" %}
{% load static %}

{% block content %}
	<div id="adminPage" class="content-container">
		<h1>Control Panel</h1>
		<p>You made it!</p>
	</div>
{% endblock %}
```
5. In `/app/app/urls.py` file, change the core contents of it to instead the following. Commenting out of the box admin functionality for now and replacing with a landing page of our own.
```
# from django.contrib import admin
from django.urls import path
from controllers import home, admin


urlpatterns = [
    # path('admin/', admin.site.urls),
    path('', home.index),
    path('admin/', admin.index)
]
```
6. Launch server and verify that going to `http://127.0.0.1:8000/admin` sends you to our page:
<img width="1266" alt="admin page works" src="https://user-images.githubusercontent.com/7783699/113872289-c2d13d00-9781-11eb-9a51-0dab82a590a5.png">
<img width="636" alt="project directory after admin creation" src="https://user-images.githubusercontent.com/7783699/113873066-75a19b00-9782-11eb-8f22-1689bbb55e10.png">

## Create new Controller, View, AND Model (given current setup)
1. Create new folder under `/app/` called `models`
2. Under `/app/models/` create new file called `__init__.py` and paste this in it: `from users import *`.
3. Create new file under `/app/models/` called `users.py`. This will be our users model.
4. In `/app/app/settings.py` add the following: `AUTH_USER_MODEL = 'accounts.Users'` under your DATABASES block.
5. In `/app/app/urls.py` replace what exists with the following:
```
# from django.contrib import admin
from django.urls import path
from controllers import home, admin, users


urlpatterns = [
    # path('admin/', admin.site.urls),
    path('', home.index),
    path('admin/', admin.index),
    path('users/', users.index)
]
```
6. Create folder called `users` under both `/app/views/` and `/app/controllers/`.
7. Under the `/app/views/users/` directory, create a file called `index.html` that contains the following:
```
{% extends "../layout/base.html" %}
{% load static %}

{% block content %}
    <div id="userPage" class="content-container">
        <h1>Users</h1>
        <table>
	    <caption class="sr-only">All Users</caption>
	    <thead>
	        <tr>
		    <th>Username</th>
		    <th>First Name</th>
		    <th>Last Name</th>
		    <th>Role</th>
	        </tr>
	    </thead>
	    <tbody>

	    </tbody>
        </table>
    </div>
{% endblock %}
```
8. Under the `/app/controllers/users/` directory, create a file called `views.py` that contains the following:
```
from django.shortcuts import render


def index(request):
    """Placeholder index view"""
    return render(request, 'users/index.html')
```
9. Create a file under that same directory (`/app/controllers/users/`) and create a file called `__init__.py` that contains the following:
```
from .views import *
```
10. (Optional) In your CSS file at `/app/assets/css/styles.css` inject the following to the bottom of it:
```
table {
    background-color: #fff;
    border: 1px solid #ccc;
    width: 100%;
}
table th {
    background-color: #ddd;
    vertical-align: middle;
}
table th,
table td {
    border: 1px solid #ccc;
    padding: .5em;
    text-align: center;
}
table tbody tr:nth-child(odd) {
    background-color: #fff;
}
table tbody tr:nth-child(even) {
    background-color: #f8f8f8;
}
.sr-only {
    border: 0;
    clip: rect(0 0 0 0);
    height: 1px;
    margin: -1px;
    overflow: hidden;
    padding: 0;
    position: absolute;
    width: 1px;
}
caption.sr-only {
    position: static;
}
```
11. Verify it all looks good. {{ADD ON TO THIS}}
<img width="632" alt="project directory after model add" src="https://user-images.githubusercontent.com/7783699/113966252-bfc76280-97fc-11eb-8205-46795272b2dd.png">

