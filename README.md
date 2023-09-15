<!-- Output copied to clipboard! -->

<!-----

You have some errors, warnings, or alerts. If you are using reckless mode, turn it off to see inline alerts.
* ERRORs: 0
* WARNINGs: 0
* ALERTS: 4

Conversion time: 2.297 seconds.


Using this Markdown file:

1. Paste this output into your source file.
2. See the notes and action items below regarding this conversion run.
3. Check the rendered output (headings, lists, code blocks, tables) for proper
   formatting and use a linkchecker before you publish this page.

Conversion notes:

* Docs to Markdown version 1.0β34
* Fri Sep 15 2023 09:06:23 GMT-0700 (PDT)
* Source doc: FastAPI: Simple application structure from scratch
* This document has images: check for >>>>>  gd2md-html alert:  inline image link in generated source and store images to your server. NOTE: Images in exported zip file from Google Docs may not appear in  the same order as they do in your doc. Please check the images!

----->


<p style="color: red; font-weight: bold">>>>>>  gd2md-html alert:  ERRORs: 0; WARNINGs: 0; ALERTS: 4.</p>
<ul style="color: red; font-weight: bold"><li>See top comment block for details on ERRORs and WARNINGs. <li>In the converted Markdown or HTML, search for inline alerts that start with >>>>>  gd2md-html alert:  for specific instances that need correction.</ul>

<p style="color: red; font-weight: bold">Links to alert messages:</p><a href="#gdcalert1">alert1</a>
<a href="#gdcalert2">alert2</a>
<a href="#gdcalert3">alert3</a>
<a href="#gdcalert4">alert4</a>

<p style="color: red; font-weight: bold">>>>>> PLEASE check and correct alert issues and delete this message and the inline alerts.<hr></p>



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
