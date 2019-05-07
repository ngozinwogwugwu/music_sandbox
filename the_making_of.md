# Making a full stack app
I've worked with full stack apps before, but I want to try deploying my own full stack app

## Resources
- [DRF tutorial])(https://www.django-rest-framework.org/tutorial/quickstart/)


## kicking it off
``` bash
cd repos/music_sandbox/backend/
source env/bin/activate
python3 manage.py runserver

cd repos/music_sandbox/frontend
npm start
```

### Backend
``` bash
# general setup
mkdir backend
cd backend
virtualenv env
source env/bin/activate

# get the django stuff
pip install django
pip install djangorestframework
pip install django-cors-headers

# start the project, start the app
django-admin startproject music_sandbox .
cd music_sandbox
django-admin startapp api
cd ..

# database migration
python3 manage.py migrate
python3 manage.py createsuperuser --email ngozi.n.nwogwugwu@gmail.com --username ngozinwogwugwu

# make a serializers file
touch music_sandbox/api/serializers.py
```
- copy/paste the code from the [DRF tutorial serializers section](https://www.django-rest-framework.org/tutorial/quickstart/#serializers)
- same with the [views section](https://www.django-rest-framework.org/tutorial/quickstart/#views)
  - make sure to change `backend.quickstart` to `music_sandbox.api`
- and [URLs section](https://www.django-rest-framework.org/tutorial/quickstart/#urls)
  - make sure to change `backend.quickstart` to `music_sandbox.api`
- to `music_sandbox/settings.py`, add:
``` python
# to INSTALLED_APPS
    'rest_framework',
    'corsheaders',

# to the bottom of the file:
CORS_ORIGIN_WHITELIST = (
    'localhost:3000',
)

# to the top of MIDDLEWARE
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',

```

Let's test it out
``` bash
# testing it out
python3 manage.py runserver

# in another window:
curl -H 'Accept: application/json; indent=4' -u ngozinwogwugwu:whatever_your_password_is http://127.0.0.1:8000/users/
```
you should get this back:
```json
[
    {
        "url": "http://127.0.0.1:8000/users/1/",
        "username": "ngozinwogwugwu",
        "email": "ngozi.n.nwogwugwu@gmail.com",
        "groups": []
    }
]
```
### Frontend
``` bash
# set up a basic react app
cd repos/music_sandbox/
npx create-react-app frontend
npm install --save axios react-router-dom lodash
npm install @material-ui/core
npm install @material-ui/icons
npm install markdown-to-jsx

# get some sample react pages
cd src
curl https://raw.githubusercontent.com/mui-org/material-ui/master/docs/src/pages/getting-started/page-layout-examples/blog/Blog.js > Blog.js
curl https://raw.githubusercontent.com/mui-org/material-ui/master/docs/src/pages/getting-started/page-layout-examples/blog/Markdown.js > Markdown.js
curl https://raw.githubusercontent.com/mui-org/material-ui/master/docs/src/pages/getting-started/page-layout-examples/blog/blog-post.1.md > blog-post.1.md
curl https://raw.githubusercontent.com/mui-org/material-ui/master/docs/src/pages/getting-started/page-layout-examples/blog/blog-post.2.md > blog-post.2.md
curl https://raw.githubusercontent.com/mui-org/material-ui/master/docs/src/pages/getting-started/page-layout-examples/blog/blog-post.3.md > blog-post.3.md
```

update `index.js` so that it points to `Blog.js` by replacing all instances of `App` with `Blog`

``` bash
cd .. # get to the frontend directory
npm start
```

### Putting it together
Let's put in some buttons that request users from the backend and logs the response to console.
- Go to the [button demo](https://material-ui.com/demos/buttons/) and display the source code for the first example
- Grab the styling and post it to the styling of `Blog.js`
- Grab the button `div`s, and paste them right under the `</Toolbar>` component
- include axios: `import axios from 'axios';`
- change `Blog` function into a class
- update the `default` button to include a `handleButtonClick` function:
``` javascript
onClick={this.handleButtonClick}
````
- make `handleButtonClick` as part of the class:
``` javascript
  handleButtonClick = () => {
    axios({
      method:'get',
      url: 'http://127.0.0.1:8000/users/',
      responseType:'json',
      auth: {
        username: 'ngozinwogwugwu',
        password: ''
      }
    }).then((response) => {
      console.log(response);
    }).catch((error) => {
      console.log(error);
    })
  };
```
- you'll probably get a **403** error back. Just update your user password in the `handleButtonClick` axios function to whatever your superuser password is, and you should get back a real response:
``` javascript
{data: Array(1), status: 200, statusText: "OK", headers: {…}, config: {…}, …}
```

Because [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) doesn't allow web pages to make requests to things without sharing an IP and port, you need to use the CORS headers module on the backend and whitelist your frontend IP/port. [Instructions here](https://pypi.org/project/django-cors-headers/).


