# MSDP

This repository contain MSDP APIs

To develop or test the APIs, we need docker installed and also git-bash terminal if it is not Linux flavored OS terminal.

## 

firstly change the volume paths metioned in compose.yml as per your system

## Useful commands
The **run.sh** is an absolute control point, where there are commands to run tests, run APIs and also applying DB migrations using Liquibase. Some of the useful command are

`>sh run.sh tests` This command will run tests and print test coverage for the code in `api` directory

`>sh run.sh tests NO_DB_SHUTDOWN` This will again run the tests and print test coverage report, but this time it will not shutdown the DB at the end. This is needed when you want to apply DB migration from feature branch to master branch, because that's what happen in prod after first release. So first checkout the master, run this command and than checkout the feature branch and run this command again to validate the liquibase will not throw any error on prod.

`>sh run.sh apis` This command will run fastapi sever exposing them on 127.0.0.1:8000

>If you see a **connection refused error** while using any of the above command, try again, sometimes the Postgres containeris not ready to accept the connections when liquibase or apis container are tryinn to connect.

>**Note:** In future any repetitive thing/command should be added in the `run.sh` and than run.sh should be used to save development time.
## Architecture
The local development IDE could be anything the developer likes. As we use docker-compose to manage the application and everything from APIs, Liquibase to Postgres are running as a container.


## To Run The Project On LINUX OS

use command 

`> sudo ./run.sh apis` in some linux system ./ does not works so you can use 
`> sudo bash run.sh apis` it will work on every linux flavour 



## ############################################################################################################################ ##

## Project Overview:----------

This project is a FastAPI-based backend service with various APIs supporting authentication, asset management,asset model management, RBAC, notifications, and more. It leverages Azure services, Key Vault for secure database connections, Docker for containerization, and Liquibase for database versioning.

use command 

`tree /F` in cmd to see the complete project folder and files 

Tools and Technologies:
Python, FastAPI, Docker, Azure CLI (if deploying to Azure), uvicorn etc.


## Setup and Installation:--------

**Clone the repository !**

    git clone <repository-url>
    cd <project-directory>


**Create a virtual environment and activate it !**
    python -m venv venv
    source venv/bin/activate   # For Mac/Linux
    venv\Scripts\activate      # For Windows

    pip install -r requirements.txt


**Running APP Locally !**
    python main.py

**Contributing !**
    Fork the repository
    Create a new branch (git checkout -b feature-branch)
    Commit changes (git commit -m "Added feature")
    Push to the branch (git push origin feature-branch)
    Open a Pull Request


## Project Workflow & Entrypoints---------------------------------------

**Startup !**
    main.py- This is the entry point and initializes the FastAPI application and sets up routes, middleware, and configurations(All configuration from core/config.py is loaded)

    Then in config.py all type of credentials been fetch from .env and we made a flag DEV_ENV:

        DEV_ENV = True → Runs the project in local development mode (bypassing Azure functions and middleware and RBAC and).Fetch databse credentials from the env and use it to establish the connection.

        DEV_ENV = False → Runs the project in production mode(enabling all hosted services).This time establish database connection using the credentials from Azure key vault for more safety reasons.


    Routes from api/ are loaded and included.
    After this all Uvicorn server starts, serving the application.


**API Workflow !**
    When a request is made to an API endpoint (e.g., /account/details), the following steps occur:

    Request Handling (via FastAPI)

    The request comes in through FastAPI (main.py).
    FastAPI routes it to the correct endpoint in api/. Routing & Business Logic

    Each API category has its own directory in api/ (e.g., api/account/account.py).
    The request is processed by the corresponding function in the module.

    Database Interaction
        If the API requires database access, it calls helper functions from utils/database_helper.py.
        If DEV_ENV=False, it uses Azure Key Vault for secure database connection retrieval.

    Response Generation
        After processing, the API returns a JSON response to the client.
        FastAPI automatically serializes Python objects into JSON format.

### **Standard API Response Format**

All APIs in this project follow a consistent response format to ensure clarity and easy integration. Below is the standardized response structure you can expect from the APIs:

1. **When Data is Found**  
   If the requested data is successfully retrieved, the API will return a `200 OK` response with the following format:
   ```json
   {
       "status": true,
       "message": "Data Found",
       "data": <result>
   }
   
**How Database Updates Work !**
    The database schema is stored in db_changelog/ (JSON files).
    Liquibase is used to apply schema changes when required.

**Dockerized Workflow !**
    If you’re running the project in Docker, the entrypoint is different:
        Entrypoint: run.sh
        Docker runs run.sh, which starts the application inside the container.
        The application runs inside a lightweight Python environment.



**Authentication Workflow !**
    The request is intercepted by middleware.
    The JWT token details is extracted and validated and request state has been modified for user.
    The system checks RBAC permissions (api/rbac/rbac.py).
    If the user is authorized, the request proceeds; otherwise, an error is returned.



---
**Ready for Production and Staging Development/Deployment!** 




