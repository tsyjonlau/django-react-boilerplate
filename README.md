# django-react-boilerplate

A boilerplate for developing web applications using Django and React.

Further details on tech stack
- **Back-end**: Django
- **Front-end**: React 16.8+, Redux toolkit, webpack, Babel/ES6,
- **Database**: PostgreSQL for running app, SQLite for testing
- **Dependency managers**: poetry (back-end), yarn (front-end)
- **Linting**: flake8 (back-end), eslint (front-end)
- **Testing**: django.test (back-end), jest and @react/testing-library (front-end)

**Key Insight**: While this fullstack boilerplate was an educational project, the experience highlighted the complexities of integrating backend and frontend into a single server. For practical development, consider using separate servers for better modularity and ease of maintenance.

## Setup back-end

#### Install python
https://www.python.org/downloads/
After installation, add python executable directory to PATH.

#### Create a virtual environment
https://docs.python.org/3/library/venv.html

#### (Bonus step) Upgrade pip and setuptools
```
pip install -U pip setuptools
```

#### Install python packages with Poetry
Note that the current poetry.toml prevents Poetry to create a new environment when installing packages.
```
pip install poetry
cd src
poetry config virtualenvs.create false --local
poetry install
```

#### Install PostgreSQL
https://www.postgresql.org/download/
After initializing PostgreSQL, remember the following information:
- USER: user of your database
- NAME: name of your database
- PASSWORD: password of your database

#### Configure PostgreSQL with Django
The following supposes that the DATABASES variable is correctly set as follows:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'OPTIONS': {
            'service': 'my_service',
            'passfile': '.my_pgpass',
        },
    },
}
```

Create in a convenient location a file named `.pg_service.conf` with the following content:
```
[my_service]
host=localhost
user=USER
dbname=NAME
password=PASSWORD
port=5432
```
This file enables Django to log into the database.

Create in the same convenient location a file named `.my_pgpass` with the following content:
```
localhost:5432:USER:NAME:PASSWORD
```
This file enables you to log into the database using command line without entering the password.

Add to the following environment variables to your system
```
PGPASSFILE = your/convenient/location/.my_pgpass
PGSERVICEFILE = your/convenient/location/.pg_service.conf
```

#### Connect to database
```
psql -U postgres
```

#### Generate new migrations
```
python manage.py makemigrations
```

#### Migrate database
```
python manage.py migrate
```

#### Run web server in development
Note that this runs on http://localhost:8000.
```
python manage.py runserver
```

#### Create superuser for admin website
```
python manage.py createsuperuser
```

## Setup front-end

#### Install node.js
https://nodejs.org
After installation, add node executable directory to PATH.

#### (Bonus step) Upgrade npm
```
npm i -g npm
```

#### Install JS dependencies with Yarn
```
npm i -g yarn
cd src/frontend
yarn
```

#### Run dedicated web server for serving front-end in development mode
Note that this runs on http://localhost:3000.
For production we will serve front-end from the same domain. Refer to next section.
```
# in frontend folder
yarn start
```

#### Linting and testing

## Linting code
```
# in frontend folder
yarn lint

# in root folder
flake8 .
```

## Running tests
In this boilerplate, there is only one Django project (`main`)
```
python manage.py test main

# in frontend folder
yarn test
```

## Setup for production
We use Render to deploy the application. The following steps are just here to explain what really happens when Render deploys the app.

#### Build front-end for production
```
# in frontend folder
yarn build
```

#### Generate static files
This collects all static files specified in the `STATICFILES_DIRS` setting.
```
python manage.py collectstatic
```

#### Run web server in production
We are using gunicorn
```
gunicorn -b 127.0.0.1:8000 -e DJANGO_SETTINGS_MODULE=backend.settings_production backend.wsgi:application
```
