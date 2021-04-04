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

## Make Tweaks to File Structure
### Move static files to parent directory for all and rename "assets".
1. Move current `/static/` from within your `hello_world` directory up one level so all apps can access it.
2. Rename it `assets` and create 3 new folder underneath it: "css", "js", and "images".
3. If using the example git project from earlier, move the brownies image under images.
4. Go to the `settings.py` under `/app/app/settings.py` and at the very bottom where it says `STATIC_URL = '/static/'`, replace that with the following:
```
STATIC_URL = 'assets/'

STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "assets")
]
```
5. If using the example git project from earlier, go to where the brownies image is references under app/hello_world/templates/index.html and replace the source of the image from `src="{% static 'coconut_bars.jpg' %}"` to `src="{% static 'images/coconut_bars.jpg' %}"`. Then that's it. Going forward all assets will exist in this one `assets` folder and just need to reference `css/`, `js/`, or `image/` when adding it.

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
5. Reload server and verify the home page despite being in a new directory still loads as if nothing has happened.

### Change all Mention of "Hello World" to "Home" and make Controllers directory where all the backend code will live
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

## Wrap Project in Docker Container
1. Install Docker. Couple ways to do this, but I downloaded https://www.docker.com/products/docker-desktop which includes it.
2. Next open your code editor (Atom, VSCode, etc.) and open new workspace for that project folder (ex. "demo-django-hello-world-starter").
3. Create a new file called `Dockerfile`.
4. 



## Deploy Container to Kubernetes (Purely command-line)
1.

