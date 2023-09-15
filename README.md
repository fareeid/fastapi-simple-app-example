# **FastAPI: Simple application structure from scratch**

[#fastapi#python](https://dev.to/t/fastapi)

In this blog post, we will set up a simple [FastAPI](https://fastapi.tiangolo.com/) application from scratch. This can serve as a good starting point for small to medium projects.


## **Series Content 📖**



* Part 1: Laying the foundation (this post)
* [Part 2](https://dev.to/alexvanzyl/fastapi-simple-application-structure-from-scratch-part-2-3lng): Migrations
* [Part 3](https://dev.to/alexvanzyl/fastapi-simple-application-structure-from-scratch-part-3-4398): Dockerize


## **What will we cover in this post? 📝**



* Generate a base project with [Poetry](https://python-poetry.org/).
* Install FastAPI, SQLAlchemy and other dependencies.
* Create the necessary files that will serve as the base of the application


## **Before getting started... ⚠️**

I am going to make the following assumptions:



* that you have a basic understanding of Python and Python [types](https://docs.python.org/3/library/typing.html);
* that you already have the below installed:
    * [Python](https://www.python.org/downloads/) 3.6+ (I am using Python 3.8)
    * [PostgreSQL](https://www.postgresql.org/download/); and
    * [Poetry](https://python-poetry.org/docs/#installation)

Without further ado, let's get started 🙂


## **Create a base project with Poetry 🏃**

Open up a terminal and enter the below command.

poetry new app

After running the above command a new directory called `app` has been created. Let's go ahead and `cd` into that directory now.

cd app

The directory structure should look like the below.

.

├── app

│   └── __init__.py

├── pyproject.toml

├── README.rst

└── tests

    ├── __init__.py

    └── test_app.py

Let's quickly go over what we have here.

The `app` directory is our main python package.

A directory with a `__init__.py` file in it is considered a package in Python. Any `.py` files we add to this directory will be considered modules of this package. Usually, this file is empty but in this case, Poetry has gone ahead and added `__version__ = '0.1.0'`.

The `pyproject.toml` file is where all our dependencies will be added to. Later on, when we start installing our dependencies you will notice a `poetry.lock` file will be created, more on that later.

The `README` file can be used to add details about the project or any useful instructions that will help other developers working on the project. Personally, I prefer writing documentation in Markdown over reStructuredText so I will go ahead and rename `README.rst` to `README.md`. Feel free to do the same or leave it as is.

mv README.rst README.md

Finally, we have our `tests` directory that contains all the unit tests.


## **Install FastAPI and other dependencies 📦**

In this section, we will install only the required dependencies to get a basic CRUD ( Create, Read, Update, Delete) application going.


### **What we will be installing?**



* FastAPI - this goes without saying 🙂
* [SQLAlchemy](https://www.sqlalchemy.org/) Object Relational Mapper (ORM)
* [psycopg2-binary](https://www.psycopg.org/) PostgreSQL database adapter
* [Uvicorn](https://www.uvicorn.org/) a lightning-fast ASGI server

poetry add fastapi sqlalchemy psycopg2-binary uvicorn

We will also install the following development dependencies, mainly to maintain code quality and for testing.



* [pytest](https://docs.pytest.org/en/latest/) testing framework
* [Mypy](http://mypy-lang.org/) static type checker for Python
* [sqlalchemy-stubs](https://github.com/dropbox/sqlalchemy-stubs) Mypy plug-in and type stubs for SQLAlchemy
* [Flake8](https://flake8.pycqa.org/en/latest/#) for code linting
* [autoflake](https://github.com/myint/autoflake) removes unused imports and unused variables
* [isort](https://github.com/timothycrosley/isort) sort import statements
* [black](https://pypi.org/project/black/) Python code formatter

poetry add -D mypy sqlalchemy-stubs flake8 autoflake isort black 

Note: We have not included `pytest` in the above command as it is usually installed by default when using Poetry to create a new project.

At this point, nothing has really changed in our directory structure but you will notice that the `pyproject.toml` file has been updated and a new `poetry.lock` file has been created. The `poetry.lock` file locks the installed dependencies to a specific version. This is in particular helpful when multiple developers are working on the same project, to ensure everyone is using the same versions of each package.

To recap our directory structure should look something like this now.

.

├── app

│   └── __init__.py

├── poetry.lock

├── pyproject.toml

├── README.md

└── tests

    ├── __init__.py

    └── test_app.py


## **Add project files 📄**

In this section, we will start adding the files that will make up the base of our application.

The first file we will create is the `main.py` file, it will serve as the entry point to our application and house all our routes. Let's create this file now under the `app` package directory.


### **main.py**


```
app/main.py
```


from fastapi import FastAPI

app = FastAPI()

@app.get("/")

def index():

    return {"message": "Hello world!"}

At this point, we actually have a basic application that we can run. If we switch back to our terminal and run the following commands.

poetry shell

uvicorn app.main:app

Tip: If you want the server to reload on file changes you can use the `--reload` flag, like so `uvicorn app.main:app --reload`

Now if we head over to a browser and hit [http://127.0.0.1:8000](http://127.0.0.1:8000/) we will be greeted with `{"message":"Hello world!"}`. FastAPI also gives us API documentation out of the box so if you now navigate to [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) you will now see the Swagger UI. Pretty awesome, right! 🤘



<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image1.png "image_tooltip")


We will come back later and update the `main.py` file but for now, let's hit `Ctrl+C` in the terminal to stop Uvicorn and continue adding the rest of our files.

Next, let's create the `db.py` under the same directory. This file will contain our database session and a base class that all models will extend from.


### **db.py**


```
app/db.py
```


from typing import Any

from sqlalchemy import create_engine

from sqlalchemy.ext.declarative import as_declarative

from sqlalchemy.orm import sessionmaker

from .config import settings

engine = create_engine(settings.SQLALCHEMY_DATABASE_URI, pool_pre_ping=True)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

@as_declarative()

class Base:

    id: Any

Info: You can read more about the `sessionmaker` function [here](https://docs.sqlalchemy.org/en/13/orm/session_basics.html) and `as_declarative` decorator [here](https://docs.sqlalchemy.org/en/13/orm/extensions/declarative/api.html#sqlalchemy.ext.declarative.as_declarative).

You may have noticed we import `settings` from `config` but we haven't actually created that file yet, so let's do so now.


### **config.py**


```
app/config.py
```


from typing import Any, Dict, Optional

from pydantic import BaseSettings, PostgresDsn, validator

class Settings(BaseSettings):

    POSTGRES_SERVER: str

    POSTGRES_USER: str

    POSTGRES_PASSWORD: str

    POSTGRES_DB: str

    SQLALCHEMY_DATABASE_URI: Optional[PostgresDsn] = None

    @validator("SQLALCHEMY_DATABASE_URI", pre=True)

    def assemble_db_connection(cls, v: Optional[str], values: Dict[str, Any]) -> Any:

        if isinstance(v, str):

            return v

        return PostgresDsn.build(

            scheme="postgresql",

            user=values.get("POSTGRES_USER"),

            password=values.get("POSTGRES_PASSWORD"),

            host=values.get("POSTGRES_SERVER"),

            path=f"/{values.get('POSTGRES_DB') or  ''}",

        )

    class Config:

        case_sensitive = True

        env_file = ".env"

settings = Settings()

Info: When loading configurations from a `.env` file the [python-dotenv](https://github.com/theskumar/python-dotenv) package is required.

Here we are making use of Pydantic's [settings management](https://pydantic-docs.helpmanual.io/usage/settings/). By default, the `BaseSettings` class will try to read the environment variables set at system level using [os.environ](https://docs.python.org/3/library/os.html#os.environ). However in our case instead we are specifying that we would like our environment variables to be read from a `.env` file. Pydantic relies on the [python-dotenv](https://github.com/theskumar/python-dotenv) package to achieve this, let's add it as a dependency now.

poetry add python-dotenv

And now we will create the `.env` file at the root of the project directory.


### **.env**


```
.env
```


# PostgreSQL

POSTGRES_SERVER=localhost

POSTGRES_USER=postgres

POSTGRES_PASSWORD=password

POSTGRES_DB=app

Make sure to edit this file to reflect your set up.


### **.gitignore**

Since the `.env` file can contain sensitive information we wouldn't want to commit this to version control. So now would probably be a good time to add a `.gitignore` file to our project. We will copy the Python `.gitignore` template provided by GitHub [here](https://github.com/github/gitignore/blob/master/Python.gitignore). \
`.gitignore`

wget https://raw.githubusercontent.com/github/gitignore/master/Python.gitignore

mv Python.gitignore .gitignore

Next, we will create a `models.py` and `schemas.py` file.


### **models.py**

The `models.py` file will contain all our models that extend from the SQLAlchemy `Base` class we defined in `db.py` We will create that file now with an example `User` model.


```
app/models.py
```


from uuid import uuid4

from sqlalchemy import Column, String, Text

from sqlalchemy.dialects.postgresql import UUID

from .db import Base

class Post(Base):

    __tablename__ = "posts"

    id = Column(UUID(as_uuid=True), primary_key=True, index=True, default=uuid4)

    title = Column(String)

    body = Column(Text)


### **schemas.py**

Let's create the `schemas.py` file now. This file will contain all our [Pydantic models](https://pydantic-docs.helpmanual.io/usage/models/). Under the hood, FastAPI makes use of these models to validate the incoming request body, parse the response body and generate [automatic docs](https://fastapi.tiangolo.com/tutorial/body/#automatic-docs) for our API. Really cool, at least I think so! 👌


```
app/schemas.py
```


from typing import Optional

from pydantic import BaseModel, UUID4

# Shared properties

class PostBase(BaseModel):

    title: Optional[str] = None

    body: Optional[str] = None

# Properties to receive via API on creation

class PostCreate(PostBase):

    title: str

    body: str

# Properties to receive via API on update

class PostUpdate(PostBase):

    pass

class PostInDBBase(PostBase):

    id: Optional[UUID4] = None

    class Config:

        orm_mode = True

# Additional properties to return via API

class Post(PostInDBBase):

    pass

# Additional properties stored in DB

class PostInDB(PostInDBBase):

    pass

The final file we will create for now is the `actions.py` file. This file will contain all our use cases or actions that will be performed, such as CRUD operations.


### **actions.py**


```
app/actions.py
```


from typing import Any, Dict, Generic, List, Optional, Type, TypeVar, Union

from fastapi.encoders import jsonable_encoder

from pydantic import UUID4, BaseModel

from sqlalchemy.orm import Session

from . import schemas

from .db import Base

from .models import Post

# Define custom types for SQLAlchemy model, and Pydantic schemas

ModelType = TypeVar("ModelType", bound=Base)

CreateSchemaType = TypeVar("CreateSchemaType", bound=BaseModel)

UpdateSchemaType = TypeVar("UpdateSchemaType", bound=BaseModel)

class BaseActions(Generic[ModelType, CreateSchemaType, UpdateSchemaType]):

    def __init__(self, model: Type[ModelType]):

        """Base class that can be extend by other action classes.

           Provides basic CRUD and listing operations.


<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: Definition &darr;&darr; outside of definition list. Missing preceding term(s)? </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


        :param model: The SQLAlchemy model


<p id="gdcalert3" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: Definition &darr;&darr; outside of definition list. Missing preceding term(s)? </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert4">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


        :type model: Type[ModelType]

        """

        self.model = model

    def get_all(

        self, db: Session, *, skip: int = 0, limit: int = 100

    ) -> List[ModelType]:

        return db.query(self.model).offset(skip).limit(limit).all()

    def get(self, db: Session, id: UUID4) -> Optional[ModelType]:

        return db.query(self.model).filter(self.model.id == id).first()

    def create(self, db: Session, *, obj_in: CreateSchemaType) -> ModelType:

        obj_in_data = jsonable_encoder(obj_in)

        db_obj = self.model(**obj_in_data)  # type: ignore

        db.add(db_obj)

        db.commit()

        db.refresh(db_obj)

        return db_obj

    def update(

        self,

        db: Session,

        *,

        db_obj: ModelType,

        obj_in: Union[UpdateSchemaType, Dict[str, Any]]

    ) -> ModelType:

        obj_data = jsonable_encoder(db_obj)

        if isinstance(obj_in, dict):

            update_data = obj_in

        else:

            update_data = obj_in.dict(exclude_unset=True)

        for field in obj_data:

            if field in update_data:

                setattr(db_obj, field, update_data[field])

        db.add(db_obj)

        db.commit()

        db.refresh(db_obj)

        return db_obj

    def remove(self, db: Session, *, id: UUID4) -> ModelType:

        obj = db.query(self.model).get(id)

        db.delete(obj)

        db.commit()

        return obj

class PostActions(BaseActions[Post, schemas.PostCreate, schemas.PostUpdate]):

    """Post actions with basic CRUD operations"""

    pass

post = PostActions(Post)

Before going back and updating our `main.py` file, let's review our final directory structure.

.

├── app

│   ├── actions.py

│   ├── config.py

│   ├── __init__.py

│   ├── main.py

│   ├── models.py

│   └── schemas.py

├── poetry.lock

├── pyproject.toml

├── README.md

└── tests

    ├── __init__.py

    └── test_app.py

Let's update our `main.py` file now and connect all the dots.


### **Update main.py**


```
app/main.py
```


from typing import Any, List

from fastapi import Depends, FastAPI, HTTPException

from pydantic import UUID4

from sqlalchemy.orm import Session

from starlette.status import HTTP_201_CREATED, HTTP_404_NOT_FOUND

from . import actions, models, schemas

from .db import SessionLocal, engine

# Create all tables in the database.

# Comment this out if you using migrations.

models.Base.metadata.create_all(bind=engine)

app = FastAPI()

# Dependency to get DB session.

def get_db():

    try:

        db = SessionLocal()

        yield db

    finally:

        db.close()

@app.get("/")

def index():

    return {"message": "Hello world!"}

@app.get("/posts", response_model=List[schemas.Post], tags=["posts"])

def list_posts(db: Session = Depends(get_db), skip: int = 0, limit: int = 100) -> Any:

    posts = actions.post.get_all(db=db, skip=skip, limit=limit)

    return posts

@app.post(

    "/posts", response_model=schemas.Post, status_code=HTTP_201_CREATED, tags=["posts"]

)

def create_post(*, db: Session = Depends(get_db), post_in: schemas.PostCreate) -> Any:

    post = actions.post.create(db=db, obj_in=post_in)

    return post

@app.put(

    "/posts/{id}",

    response_model=schemas.Post,

    responses={HTTP_404_NOT_FOUND: {"model": schemas.HTTPError}},

    tags=["posts"],

)

def update_post(

    *, db: Session = Depends(get_db), id: UUID4, post_in: schemas.PostUpdate,

) -> Any:

    post = actions.post.get(db=db, id=id)

    if not post:

        raise HTTPException(status_code=HTTP_404_NOT_FOUND, detail="Post not found")

    post = actions.post.update(db=db, db_obj=post, obj_in=post_in)

    return post

@app.get(

    "/posts/{id}",

    response_model=schemas.Post,

    responses={HTTP_404_NOT_FOUND: {"model": schemas.HTTPError}},

    tags=["posts"],

)

def get_post(*, db: Session = Depends(get_db), id: UUID4) -> Any:

    post = actions.post.get(db=db, id=id)

    if not post:

        raise HTTPException(status_code=HTTP_404_NOT_FOUND, detail="Post not found")

    return post

@app.delete(

    "/posts/{id}",

    response_model=schemas.Post,

    responses={HTTP_404_NOT_FOUND: {"model": schemas.HTTPError}},

    tags=["posts"],

)

def delete_post(*, db: Session = Depends(get_db), id: UUID4) -> Any:

    post = actions.post.get(db=db, id=id)

    if not post:

        raise HTTPException(status_code=HTTP_404_NOT_FOUND, detail="Post not found")

    post = actions.post.remove(db=db, id=id)

    return post

Finally, if we run the server again and hit [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) we now have a basic API that can perform CRUD operations on our Post entity. 🚀

uvicorn app.main:app



<p id="gdcalert4" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image2.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert5">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image2.png "image_tooltip")



## **Conclusion 💡**

If you have made it this far, well done! 👍

We created a simple application that can serve as a good starting point for small to medium projects. There are still a number of things we can include in this base project such as migrations or adding Docker to our stack. (_Hint: we will cover this in future posts, stay tuned 😉_)

The final code for this post can be found on [GitHub](https://github.com/alexvanzyl/fastapi-simple-app-example/tree/part-1).

If you enjoyed reading this article and would like to stay tuned for more, or just want to connect, follow me on twitter [@alexvanzyl](https://twitter.com/alexvanzyl).




# **FastAPI: Simple application structure from scratch - Part 2**

[#fastapi#python](https://dev.to/t/fastapi)

Continuing where we left off in part one of this series, we will add migrations to our project using Alembic.


## **Series Content 📖**



* [Part 1](https://dev.to/alexvanzyl/fastapi-simple-application-structure-from-scratch-2mem): Laying the foundation
* Part 2: Migrations (this post)
* Part 3: Dockerize

Warning: To avoid any issues, make sure to drop the posts table from your database and run `poetry install` again.


## **What we will cover in this post? 📝**



* What is Alembic?
* Install Alembic
* Restructure project to support auto migrations
* Create a `migrations` directory
* Configure Alembic
* Generate migration file


## **What is Alembic? 🤔**

From Alembic's GitHub [repository](https://github.com/sqlalchemy/alembic).

Alembic is a database migrations tool written by the author of [SQLAlchemy](http://www.sqlalchemy.org/).


## **Install Alembic**

Since we will be running many commands in our virtual environment, let's switch that shell now.

poetry shell  

The first thing we need to do is add Alembic as a dependency.

poetry add alembic  


## **Create a migrations directory 📁**

Now that we have Alembic installed let's go ahead and generate the `migrations` directory.

alembic init migrations  

After executing the above command a `migrations` directory and `alembic.ini` file will be generated. Our new project structure will look like this now.

.

├── alembic.ini

├── app

├── migrations

├── mypy.ini

├── poetry.lock

├── pyproject.toml

├── README.md

└── tests

The `alembic.ini` file holds the configurations parameter for Alembic, such as the path to our migration scripts. Let's also go over the content of the `migrations` directroy.

migrations

├── env.py

├── README

├── script.py.mako

└── versions

The `env.py` file reads the configurations from the `alembic.ini` and handles running the migration scripts. We can also edit this file and tweak to our needs, something we will do later.

The `README` file can be used to add informative details about our migrations. We will leave this file as-is is for now.

The `script.py.mako` file is a [Mako](https://www.makotemplates.org/) template file. Alembic uses this template when generating revision files (migration scripts).

Finally, we have the `versions` directory this is where all our revision files will live.


## **Restructure project to support auto migrations**

In order for us to take advantage of Alembics [auto generated migrations](https://alembic.sqlalchemy.org/en/latest/autogenerate.html) we need to restructure our project and make some minor changes to existing files.

Let's create a new `db` directory now.

mkdir app/db

We going to add three new files to this directroy, `__init__.py`, `base.py` and `session.py`

touch app/db/__init__.py app/db/base.py app/db/session.py

What we now have is an empty `db` package. This will replace the `db.py` module and we will delete this file later.


### **Edit <code>__init__.py</code> file.</strong>


```
app/db/__init__.py
```


from .base import Base  # noqa

# Import all the models, so that the Base class 

# has them before being imported by Alembic.

from .. import models # noqa

The schema for each table is derived from the attributes specified on each model. We import all our models afterwards to make sure the `Base.metadata` attribute gets populates with all the table schemas. Alembic makes use of this when using `autogenerate`.

Info: `Base.metadata.tables` contains a collection of SQLAlchemy <code>[Table](https://docs.sqlalchemy.org/en/13/core/metadata.html#sqlalchemy.schema.Table)</code> objects.


### **Edit <code>base.py</code> file.</strong>

We going to move the original `Base` class from the `db.py` module into this file, but we will make a few changes to it.

First, we going to update our `Base` class to automatically generate the `__tablename__` attribute for or models. The name will be derived from the model class name. For example, in our case, the `Post` class would generate a `post` table.

I prefer my table names to be in plural form, so instead of `post` it would `posts`. For this, we can make use of the [inflect.py](https://github.com/jazzband/inflect) module. If you share the same preference, then let's install that now quick.

poetry add inflect


```
app/db/base.py
```


from typing import Any    

import inflect     

from sqlalchemy.ext.declarative import as_declarative, declared_attr    

p = inflect.engine()    

@as_declarative()    

class Base:    

    id: Any    

    __name__: str    

    # Generate __tablename__ automatically in plural form.   

    # i.e 'Post' model will generate table name 'posts'   

    @declared_attr    

    def __tablename__(cls) -> str:    

        return p.plural(cls.__name__.lower())

The main changes to the `Base` class are we added a new `__name__` and declarative `__tablename__` attribute.

This means we no longer need to declare the `__tablename__` attribute on our models. Let’s edit the Post model in our models.py file to reflect this change.


### **Update <code>models.py</code> file</strong>


```
app/models.py
```


from uuid import uuid4

from sqlalchemy import Column, String, Text

from sqlalchemy.dialects.postgresql import UUID

from .db.base import Base

class Post(Base):

    id = Column(UUID(as_uuid=True), primary_key=True, index=True, default=uuid4)

    title = Column(String)

    body = Column(Text)

We updated our import statement for the `Base` class and dropped the `__tablename__` attribute on the `Post` model.


### **Edit <code>session.py</code> file</strong>

Again we can copy the session details from `db.py` file into this one.


```
app/db/session.py
```


from sqlalchemy import create_engine

from sqlalchemy.orm import sessionmaker

from ..config import settings

engine =  create_engine(settings.SQLALCHEMY_DATABASE_URI, pool_pre_ping=True)

SessionLocal =  sessionmaker(autocommit=False, autoflush=False, bind=engine)


### **Update <code>main.py</code> file</strong>

We need to update our import statements on line 8 and 9. Also, let's comment out line 13 since we will be using migrations.


```
app/main.py
```


...

from . import actions, schemas

from .db.session import SessionLocal

...

# Create all tables in database.

# Comment this out if you using migrations.

# models.Base.metadata.create_all(bind=engine)

...

We can now delete the `db.py` file.

rm app/db.py


## **Configure Alembic ⚙️**


### **Update <code>alembic.ini</code> file</strong>

We going to configure our connection details in the `env.py` file. So let's open up the `alembic.ini` and comment out the line `sqlalchemy.url = driver://user:pass@localhost/dbname`, like below.


```
alembic.ini
```


...

# the output encoding used when revision files

# are written from script.py.mako

# output_encoding = utf-8

# sqlalchemy.url  = driver://user:pass@localhost/dbname

...


### **Update <code>env.py</code> file</strong>

Let's configure the `env.py` file now.

`migrations/env.py` line 21:22

...

# add your model's MetaData object here

# for 'autogenerate' support

# from myapp import mymodel

# target_metadata = mymodel.Base.metadata

from app.db import Base # noqa

target_metadata = Base.metadata

...

Note: We import the `Base` class from `app.db` and not `app.db.base` for reason explained before.

Next we will create our own function the will get the URL to our database connection.

`migrations/env.py` line 31:38

...

def get_url():

    from app.config import settings

    user = settings.POSTGRES_USER

    password = settings.POSTGRES_PASSWORD

    server = settings.POSTGRES_SERVER

    db = settings.POSTGRES_DB

    return f"postgresql://{user}:{password}@{server}/{db}"

...

All we are doing here is importing and using our database configuration settings to generate the connection URL.

To make use of this new function we need to update the `run_migrations_offline` and `run_migrations_online` functions. Let's do that now.

`migrations/env.py` line 53 and 72:78

...

def run_migrations_offline():

    ...

    url = get_url()

    ...

def run_migrations_online():

    """Run migrations in 'online' mode.

    In this scenario we need to create an Engine

    and associate a connection with the context.

    """

    configuration = config.get_section(config.config_ini_section)

    configuration["sqlalchemy.url"] = get_url()

    connectable = engine_from_config(

        configuration,

        prefix="sqlalchemy.",

        poolclass=pool.NullPool,

    )

    ...

...

The final `env.py` file should like this now.


```
migrations/env.py
```


from logging.config import fileConfig

from sqlalchemy import engine_from_config

from sqlalchemy import pool

from alembic import context

# this is the Alembic Config object, which provides

# access to the values within the .ini file in use.

config = context.config

# Interpret the config file for Python logging.

# This line sets up loggers basically.

fileConfig(config.config_file_name)

# add your model's MetaData object here

# for 'autogenerate' support

# from myapp import mymodel

# target_metadata = mymodel.Base.metadata

from app.db import Base # noqa

from app import models # noqa

target_metadata = Base.metadata

# other values from the config, defined by the needs of env.py,

# can be acquired:

# my_important_option = config.get_main_option("my_important_option")

# ... etc.

def get_url():

    from app.config import settings

    user = settings.POSTGRES_USER

    password = settings.POSTGRES_PASSWORD

    server = settings.POSTGRES_SERVER

    db = settings.POSTGRES_DB

    return f"postgresql://{user}:{password}@{server}/{db}"

def run_migrations_offline():

    """Run migrations in 'offline' mode.

    This configures the context with just a URL

    and not an Engine, though an Engine is acceptable

    here as well.  By skipping the Engine creation

    we don't even need a DBAPI to be available.

    Calls to context.execute() here emit the given string to the

    script output.

    """

    url = get_url()

    context.configure(

        url=url,

        target_metadata=target_metadata,

        literal_binds=True,

        dialect_opts={"paramstyle": "named"},

    )

    with context.begin_transaction():

        context.run_migrations()

def run_migrations_online():

    """Run migrations in 'online' mode.

    In this scenario we need to create an Engine

    and associate a connection with the context.

    """

    configuration = config.get_section(config.config_ini_section)

    configuration["sqlalchemy.url"] = get_url()

    connectable = engine_from_config(

        configuration,

        prefix="sqlalchemy.",

        poolclass=pool.NullPool,

    )

    connectable = engine_from_config(

        config.get_section(config.config_ini_section),

        prefix="sqlalchemy.",

        poolclass=pool.NullPool,

    )

    with connectable.connect() as connection:

        context.configure(

            connection=connection, target_metadata=target_metadata

        )

        with context.begin_transaction():

            context.run_migrations()

if context.is_offline_mode():

    run_migrations_offline()

else:

    run_migrations_online()


## **Generate migration file ✨**

Finally, at this point we can now generate our first migraion script.

alembic revision --autogenerate -m "Create posts table"

This will generate a new migration file in the `migrations/versions/` directory. In my case it created a file named `ee48ba03fe9f_create_posts_table.py` with the below content.


```
migrations/versions/ee48ba03fe9f_create_posts_table.py
```


"""Create posts table

Revision ID: ee48ba03fe9f

Revises: 

Create Date: 2020-05-24 15:39:50.164129

"""

import sqlalchemy as sa

from alembic import op

from sqlalchemy.dialects import postgresql

# revision identifiers, used by Alembic.

revision = "ee48ba03fe9f"

down_revision = None

branch_labels = None

depends_on = None

def upgrade():

    # ### commands auto generated by Alembic - please adjust! ###

    op.create_table(

        "posts",

        sa.Column("id", postgresql.UUID(as_uuid=True), nullable=False),

        sa.Column("title", sa.String(), nullable=True),

        sa.Column("body", sa.Text(), nullable=True),

        sa.PrimaryKeyConstraint("id"),

    )

    op.create_index(op.f("ix_posts_id"), "posts", ["id"], unique=False)

    # ### end Alembic commands ###

def downgrade():

    # ### commands auto generated by Alembic - please adjust! ###

    op.drop_index(op.f("ix_posts_id"), table_name="posts")

    op.drop_table("posts")

    # ### end Alembic commands ###

To run this migration now all we need to do is run the following command.

alembic upgrade head

We can also roll back changes by running the below.

alembic downgrade head


## **Conclusion 💡**

Alembic is now in place to manage all our migration scripts. We had to make some minor changes to achieve this and refactored `db.py` file into multiple files that make up a package.

The final code for this post can be found on [GitHub](https://github.com/alexvanzyl/fastapi-simple-app-example/tree/part-2).

If you enjoyed reading this article and would like to stay tuned for more, or just want to connect, follow me on twitter [@alexvanzyl](https://twitter.com/alexvanzyl).




# **FastAPI: Simple application structure from scratch - Part 3**

[#fastapi#python](https://dev.to/t/fastapi)

In the third part of this series, we will containerize our application using Docker.


## **Series Content 📖**



* [Part 1](https://dev.to/alexvanzyl/fastapi-simple-application-structure-from-scratch-2mem): Laying the foundation
* [Part 2](https://dev.to/alexvanzyl/fastapi-simple-application-structure-from-scratch-part-2-3lng): Migrations
* Part 3: Dockerize (this post)


## **Prerequisites**

You will need to install:



* [Docker](https://docs.docker.com/docker-for-windows/install/); and
* [Docker Compose](https://docs.docker.com/compose/install/)


## **What we will cover in this post? 📝**



* Create a Dockerfile for our application
* Create Docker Compose files
* Update `.env` and `config.py`
* Create a `prestart.sh` file to run migrations


## **Create a Dockerfile for our application**

Our Dockerfile will contain the details of how docker should build our server image. This is where our FastAPI application will run.


```
Dockerfile
```


FROM tiangolo/uvicorn-gunicorn-fastapi:python3.8

WORKDIR /app/

# Install Poetry

RUN curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | POETRY_HOME=/opt/poetry python && \

    cd /usr/local/bin && \

    ln -s /opt/poetry/bin/poetry && \

    poetry config virtualenvs.create false

# Copy poetry.lock* in case it doesn't exist in the repo

COPY ./pyproject.toml ./poetry.lock* /app/

# Allow installing dev dependencies to run tests

ARG INSTALL_DEV=false

RUN bash -c "if [ $INSTALL_DEV == 'true' ] ; then poetry install --no-root ; else poetry install --no-root --no-dev ; fi"

# For development, Jupyter remote kernel, Hydrogen

# Using inside the container:

# jupyter lab --ip=0.0.0.0 --allow-root --NotebookApp.custom_display_url=http://127.0.0.1:8888

ARG INSTALL_JUPYTER=false

RUN bash -c "if [ $INSTALL_JUPYTER == 'true' ] ; then pip install jupyterlab ; fi"

COPY . /app

ENV PYTHONPATH=/app

Info: The base image we using is one created by the author of FastAPI, Sebastián Ramírez. See the repository on GitHub [here](https://github.com/tiangolo/uvicorn-gunicorn-fastapi-docker).

The comments in the Dockerfile provide an explanation into what happens when the image gets built. By including [JupyterLab](https://github.com/jupyterlab/jupyterlab), this gives us the ability to spin up a notebook and experiment with ideas while having full access to our FastAPI application.

Since we copy the whole context of our project into the image, it's usually a good idea to create a `.dockerignore` file. This will prevent adding any unnecessary files to our image as well as sensitive information. This helps to reduce the size of the image and prevent potential security risks.


```
.dockerignore
```


.env

.env.*

.vscode

.idea

*.egg-info

.mypy-cache

.cache

.git

.gitignore

docker-compose.*

Dockerfile

*.md


## **Create Docker Compose files**

We will use both a `docker-compose.yml` and `docker-compose.override.yml` file to orchestrate our services.

The services we will be running are PostgreSQL, pgAdmin (a graphical interface to administer PostgreSQL), and our FastAPI application server.


```
docker-compose.yml
```


version: "3.3"

services: 

  db:

    image: postgres:12

    volumes:

      - db-data:/var/lib/postgresql/data/pgdata

    env_file:

      - .env

    environment:

      - PGDATA=/var/lib/postgresql/data/pgdata

  pgadmin:

    image: dpage/pgadmin4

    depends_on:

      - db

    env_file:

      - .env

  server:

    build:

      context: ./

      dockerfile: Dockerfile

      args:

        INSTALL_DEV: ${INSTALL_DEV-false}

    depends_on:

      - db

    env_file:

      - .env

volumes:

  db-data:

All our services declare the environment variables from reading the `.env` file. We need to make adjustments to this file, which we will do shortly. For PostgreSQL, we use the official image and for pgAdmin, we use the image created by [dpage](https://www.pgadmin.org/download/pgadmin-4-container/), listed on [Docker Hub](https://hub.docker.com/).

Info: You can find the details of the PostgreSQL image [here](https://hub.docker.com/_/postgres) and pgAdmin [here](https://hub.docker.com/r/dpage/pgadmin4/)

You'll notice we haven't exposed any ports for any of our services. Usually, in production our application would be served behind some sort of proxy, for example, nginx over `https` protocol. Yet while developing it would be nice to access our services locally. This is where we can make use of the `docker-compose.override.yml` file. Let's do so now.


```
docker-compose.override.yml
```


version: "3.3"

services: 

  pgadmin:

    ports:

      - "8080:8080"

  server:

    ports:

      - "8888:8888"

      - "80:80"

    volumes:

      - ./:/app

    environment:

      - JUPYTER=jupyter lab --ip=0.0.0.0 --allow-root --NotebookApp.custom_display_url=http://127.0.0.1:8888

    build:

      context: ./

      dockerfile: Dockerfile

      args:

        INSTALL_DEV: ${INSTALL_DEV-true}

        INSTALL_JUPYTER: ${INSTALL_JUPYTER-true}

    command: /start-reload.sh

For pgAdmin it is pretty straightforward we exposing port `8080`.

The server has a bit more going on though. First, we expose the ports `80` used to serve our API and `8888` used to access JupyterLab. Also by default, we install our development dependencies and JupyterLab. For our command, we tell our server that we want to reload after each file change. The changes in files are detected because we create a volume binding between our host machine and the container.

Info: The `start-reload.sh` file is part of the base image. You can see the content of this file [here](https://github.com/tiangolo/uvicorn-gunicorn-docker/blob/master/docker-images/start-reload.sh).


## **Update <code>.env</code> and <code>config.py</code></strong>

We will update our `.env` file to include more environment variables. It should look like this now.


```
.env
```


# PostgreSQL

POSTGRES_SERVER=db

POSTGRES_USER=postgres

POSTGRES_PASSWORD=password

POSTGRES_DB=app

# PgAdmin

PGADMIN_DEFAULT_EMAIL=admin@local.host

PGADMIN_DEFAULT_PASSWORD=password

PGADMIN_LISTEN_PORT=8080

We no longer need to read the `.env` file from our `config.py` file. Since the environment variables, are declared inside the running container. We will comment out line 33 in the `config.py` file.


```
app/config.py
```


...

    class Config:

        case_sensitive = True

        # If you want to read environment variables from a .env

        # file instead un-comment the below line and create the

        # .env file at the root of the project.

        # env_file = ".env"

settings = Settings()


## **Create <code>prestart.sh</code> file</strong>

By default, the base image we use for our server looks for a `prestart.sh` file. This is handy to run scripts before our application starts. We will use it to run our migrations on pre-start.


```
prestart.sh
```


#! /usr/bin/env bash

# Let the DB start

sleep 10;

# Run migrations

alembic upgrade head

Let's also give this file execution permissions.

chmod +x prestart.sh


## **Conclusion 💡**

Finally, we can now spin up our service using `docker-compose up -d`. We now can access all our services locally like before.

docker-compose up -d


### **FastAPI Swagger UI**

[http://localhost/docs](http://localhost/docs)



<p id="gdcalert5" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image3.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert6">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image3.png "image_tooltip")



### **pgAdmin**

[http://localhost:8080](http://localhost:8080/)



* email: admin@local.host
* password: password



<p id="gdcalert6" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image4.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert7">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image4.png "image_tooltip")



### **JupyterLab**

Running JypyterLab requires spinning it up inside our server container. Like so.

docker-compose exec server bash

# Inside the container

root@a09f3c12bf5e:/app# $JUPYTER

This will print a link that should look something like this `http://127.0.0.1:8888/?token=&lt;token>`



<p id="gdcalert7" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image5.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert8">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image5.png "image_tooltip")


If you have been following since part one, congratulations on making this far! 🏆

We now have an application that is fully containerized. 🐳

The final code for this post can be found on [GitHub](https://github.com/alexvanzyl/fastapi-simple-app-example/tree/part-3).

If you enjoyed reading this article and would like to stay tuned for more, or just want to connect, follow me on twitter [@alexvanzyl](https://twitter.com/alexvanzyl).
