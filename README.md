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

## Wrap Project in Docker Container
1. Install Docker. Couple ways to do this, but I downloaded https://www.docker.com/products/docker-desktop which includes it.
2. Next open your code editor (Atom, VSCode, etc.) and open new workspace for that project folder (ex. "demo-django-hello-world-starter").
3. Create a new file called `Dockerfile`.
4. 



## Deploy Container to Kubernetes (Purely command-line)


