# [Ecommerce Application](https://ecommerce-application-jyip.onrender.com/)

Original project: https://github.com/Medo-ID/Ecommerce_application

A monorepo for a full-stack Ecommerce application featuring a client side built with React and a server side with Express.js. PostgreSQL is used as the database for data persistence. This repository provides the entire stack in a single architecture to streamline development and deployment processes.

## Table of Contents

- [Ecommerce Application](#ecommerce-application)
  - [Table of Contents](#table-of-contents)
  - [Project Structure](#project-structure)
  - [Technologies Used](#technologies-used)
  - [Features](#features)
  - [Installation](#installation)
  - [Setup and Configuration](#setup-and-configuration)
  - [Running the Application](#running-the-application)
  - [Project Structure Details](#project-structure-details)
  - [License](#license)

## Project Structure

This project follows a monorepo architecture with the following structure:

```bash
Ecommerce_application
    ├── client
    │ └── public
    │ └── src
    ├── server
    │ ├── controllers
    │ ├── models
    │ │ └── tables.sql
    │ └── routes
    │ └── index.js
    └── package.json
```

## Technologies Used

- **Frontend**: React (JavaScript)
- **Backend**: Express.js (Node.js)
- **Database**: PostgreSQL
- **Monorepo Architecture**: Shared repository structure for client and server code

## Features

- **Filter Products by Category**: Users can browse products and filter them by category for a better shopping experience.
- **User Authentication**: Includes options for user login and registration.
- **GitHub Sign-Up**: Users can sign up quickly using their GitHub account.
- **Order Placement**: Both authenticated and non-authenticated users can place orders, with additional benefits for logged-in users.
- **Cart Checkout and Payment Processing**: Integrated Stripe payment gateway allows secure checkout and payment processing.

## Installation

Follow these steps to clone the repository and install dependencies for both the client and server:

1. **Clone the Repository**:

   ```bash
   git clone https://github.com/Medo-ID/Ecommerce_application.git
   cd Ecommerce_application
   ```

2. **Install Dependencies**:

   - For monorepo we use workspaces, you can run in the root directory

   ```bash
   npm install
   ```

   **This will install dependencies for all packages defined in the workspace configuration.**

   - If you want to install new scopes and private packages in Workspaces you can run:

   ```bash
   npm install @scope/package-example --workspace "name of workspace client || server"
   ```

## Setup and Configuration

1. **Database Setup**:

   - Make sure you have PostgreSQL installed.
   - Create a new PostgreSQL database for the application.
   - Run the `tables.sql` script located in `server/models` to set up the necessary tables:

   ```sql
   \i path/to/tables.sql
   ```

   - Update your database credentials and configuration in the server code as required.

2. **Environment Variables**:

   - Create a `.env` file in the `server` folder with the following variables:

   ```env
   # server port
   PORT=<this one for development you can use 3000>

   # database url
   DB_URL=<your postgresql url for database connection>

   # express session
   SESSION_SECRET=<strong secret for session>

   # front end url
   FRONT_DOMAIN=<font end url || http://localhost:3001>

   # envirement
   SERVER_URL=<url for dev backend || http://localhost:3000>
   NODE_ENV=<development>

   # github auth 2.0 api keys
   GITHUB_CLIENT=<github client>
   GITHUB_SECRET=<github secret>

   # stripe payment api keys
   STRIPE_PUBLIC=<stripe public key>
   STRIPE_SECRET=<stripe secret key>
   ```

3. **Frontend Configuration**:
   - Ensure any necessary API URLs are configured in the client side code under `client/src`.
   - Create a `.env` file in the `client` folder with the following variables
   ```env
   PORT=3001
   REACT_APP_API_URL=<our back end url api || http://localhost:3000>
   REACT_APP_ENV=<development>
   ```

## Running the Application

- **Start the App**:
  - **To start both the client and server concurrently, use the following command in the root directory:**
    ```bash
    npm run start
    ```
    This command will run both the client and server:
    - Server: Runs on `http://localhost:3000` (as specified in `server/.env`)
    - Client: Runs on `http://localhost:3001` (as specified in `client/.env`)

## Project Structure Details

- **client/src**: Contains all React frontend code.
- **server/controllers**: Holds controller functions that manage the application logic.
- **server/models/tables.sql**: SQL script to create necessary tables for PostgreSQL.
- **server/index.js**: Entry point for the Express.js server, setting up routes and middleware.


## Docker Setup

For my DevOps practical, I Dockerized the application and ran the frontend, backend and PostgreSQL database in separate containers.

The setup is:

```text
User
 |
 v
Frontend (React + Nginx)
 |
 v
Backend (Node.js + Express)
 |
 v
PostgreSQL
 |
 v
Docker Volume
```

### Docker Files

I created Dockerfiles for both the client and server.

```text
client/
    ├── Dockerfile
    └── .dockerignore

server/
    └── Dockerfile

docker-compose.yml
```

The frontend is built using Node.js and served using Nginx.

The backend runs using Node.js and the container runs as the `node` user instead of root.

### Docker Compose

I used Docker Compose to run all three services together:

* `frontend`
* `backend`
* `postgres`

To build and start the application:

```bash
docker compose up -d --build
```

To check the containers:

```bash
docker compose ps
```

To check logs:

```bash
docker compose logs
```

### Environment Variables

I used a `.env` file for database credentials and session configuration.

The `.env` file is included in `.gitignore`, so passwords and secrets are not pushed to GitHub.

The backend uses the PostgreSQL service name from Docker Compose to connect to the database.

```text
postgres:5432
```

### Docker Network

Docker Compose creates a network for the application containers.

The frontend, backend and PostgreSQL containers communicate through this network.

I did not expose PostgreSQL port `5432` to the internet.

The backend connects to PostgreSQL using:

```text
postgresql://ecommerce_user:<password>@postgres:5432/ecommerce
```

Here `postgres` is the PostgreSQL service name in `docker-compose.yml`.

### PostgreSQL Volume

I used a named Docker volume for PostgreSQL:

```text
ecommerce-postgres-data
```

This keeps the database data even when the containers are recreated.

I tested the database before and after recreating the containers.

The product count was:

```text
15
```

before recreation and remained:

```text
15
```

after recreation.

To check the data:

```bash
docker compose exec postgres psql -U ecommerce_user -d ecommerce -c "SELECT COUNT(*) FROM products;"
```

### PostgreSQL Health Check

I added a health check for PostgreSQL using `pg_isready`.

The backend waits for PostgreSQL to become healthy before starting.

This helps when PostgreSQL takes some time to become ready after the containers are started.

### Testing

I tested the frontend using:

```bash
curl -I http://localhost:8080
```

The frontend returned:

```text
HTTP/1.1 200 OK
```

I tested the backend using:

```bash
curl http://localhost:5000/
```

I also tested the products API:

```bash
curl http://localhost:5000/api/products
```

The API returned the product data from PostgreSQL.

### AWS EC2 Deployment

I deployed the Dockerized application on an AWS EC2 instance running Ubuntu.

The application ports used are:

```text
8080 - Frontend
5000 - Backend
```

PostgreSQL port `5432` is not publicly exposed.

### Problems I Faced

While setting up the application, I faced a PostgreSQL SSL connection error:

```text
The server does not support SSL connections
```

The application was originally configured to use SSL for PostgreSQL. Since I was running PostgreSQL locally inside Docker, I changed the PostgreSQL connection configuration to:

```js
ssl: false
```

After this change, the products API worked correctly.

I also had to use the Docker Compose service name `postgres` instead of `localhost` for the backend database connection.

### What I Learned

Through this setup I learned how to:

* Dockerize an existing application
* Create Dockerfiles
* Use Docker Compose
* Connect containers using Docker networking
* Use environment variables
* Use Docker volumes for database persistence
* Add container health checks
* Run a container as a non-root user
* Deploy containers on AWS EC2
* Debug PostgreSQL and Docker connection problems
* Use Git and GitHub for version control

### GitHub Repository

My Dockerized version of the project is available here:

`https://github.com/vaishu159878/ecommerce-app`


## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

Happy coding!
