# Car Dealership Web Application

A full-stack web application for browsing car dealerships, viewing dealer details, reading customer reviews, and posting new reviews. The project combines a React frontend, a Django backend, a Node.js/Express data service backed by MongoDB, and a Flask sentiment-analysis microservice.

## Overview

This application simulates a dealership review platform where users can:

- Register and log in to the platform
- Browse dealerships by state or view all dealerships
- Open a dealer profile to see detailed information
- Read customer reviews for each dealer
- Submit a new review for a dealer
- View car inventory data grouped by make and model
- See sentiment analysis for submitted reviews

## Project architecture

The application is built as a small multi-service system:

- Frontend: React single-page app served through the Django project
- Backend API: Django application handling authentication, dealer APIs, and car inventory
- Data service: Node.js + Express + MongoDB for dealership and review data
- Sentiment microservice: Flask service that analyzes review text using NLTK

## Tech stack

### Frontend
- React
- React Router DOM
- Create React App style build pipeline

### Backend
- Python
- Django
- Django authentication and session handling
- SQLite database for car inventory and user-related models

### Data and services
- Node.js
- Express.js
- MongoDB
- Flask
- NLTK sentiment analysis

### Deployment / containerization
- Dockerfiles for the Django app and the Flask microservice
- Kubernetes deployment manifest
- Docker Compose for the MongoDB and Node API services

## Repository structure

- [README.md](README.md) – Project documentation
- [server/](server/) – Main application directory
  - [server/manage.py](server/manage.py) – Django entry point
  - [server/djangoproj/](server/djangoproj/) – Django project settings and routing
  - [server/djangoapp/](server/djangoapp/) – Main Django app with views, models, URLs, and APIs
  - [server/frontend/](server/frontend/) – React frontend source code
  - [server/database/](server/database/) – Node.js + MongoDB service
  - [server/djangoapp/microservices/](server/djangoapp/microservices/) – Flask sentiment analyzer
  - [server/requirements.txt](server/requirements.txt) – Python dependencies
  - [server/package.json](server/package.json) – Node package manifest for the server root
  - [server/deployment.yaml](server/deployment.yaml) – Kubernetes deployment config
  - [server/entrypoint.sh](server/entrypoint.sh) – Django startup script

## Main features

### Authentication
The Django backend exposes login, logout, and registration endpoints. These are handled through Django's built-in user model and authentication system.

### Dealer discovery
The application can retrieve dealership listings from the MongoDB-backed data service. Listings can be fetched for all dealers or filtered by state.

### Dealer details and reviews
Each dealer page loads details and reviews from the data service. Review results are enriched with sentiment labels from the Flask microservice.

### Car inventory
The Django app includes models for car makes and car models. When the database is empty, the app seeds sample inventory data through the populate script.

## API endpoints

The Django backend exposes the following routes:

- POST /djangoapp/login
- POST /djangoapp/logout
- POST /djangoapp/register
- GET /djangoapp/get_dealers/
- GET /djangoapp/get_dealers/<state>
- GET /djangoapp/dealer/<dealer_id>
- GET /djangoapp/reviews/dealer/<dealer_id>
- POST /djangoapp/add_review
- GET /djangoapp/get_cars/

The Node.js API exposes dealership and review data through endpoints such as:

- GET /fetchReviews
- GET /fetchReviews/dealer/:id
- GET /fetchDealers
- GET /fetchDealers/:state
- GET /fetchDealer/:id
- POST /insert_review

The Flask sentiment service exposes:

- GET /analyze/<input_txt>

## Local setup

### 1. Prerequisites
Make sure these tools are installed:

- Python 3.10+
- Node.js 18+
- npm
- Docker (optional but recommended for the full stack)

### 2. Set up the Python backend

```bash
cd server
python -m venv djangoenv
source djangoenv/bin/activate   # On Windows use: djangoenv\Scripts\activate
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver 8000
```

### 3. Build the React frontend

```bash
cd server/frontend
npm install
npm run build
```

### 4. Start the Node.js + MongoDB data service

You can use Docker Compose from the database folder:

```bash
cd server/database
docker-compose up
```

Or run the Node service directly after installing dependencies:

```bash
cd server/database
npm install
node app.js
```

### 5. Start the sentiment microservice

```bash
cd server/djangoapp/microservices
pip install -r requirements.txt
python app.py
```

The Flask service will run on port 5000 by default, while the Node data service runs on port 3030.

### 6. Configure service URLs

The Django app uses environment variables for service URLs:

- backend_url
- sentiment_analyzer_url

If you are running the services locally, you may want to set them to:

```bash
backend_url=http://localhost:3030
sentiment_analyzer_url=http://localhost:5050/
```

## Running the app

Once the services are running:

- Open the Django app at http://localhost:8000
- The React frontend is served through the Django project templates
- Dealer and review APIs are available through the Django routes above

## Data flow

1. The React frontend calls the Django backend endpoints.
2. The Django backend requests dealership and review data from the Node.js/Express API.
3. Review text is sent to the Flask sentiment microservice for analysis.
4. The Django app returns a combined response to the frontend.

## Notes

- The Django app uses SQLite by default for local development.
- The MongoDB-backed data service loads initial dealership and review data from JSON files.
- Car inventory data is seeded automatically if the related tables are empty.
- The project includes Kubernetes deployment assets for container-based deployment.

## Future improvements

Possible enhancements include:

- Replacing the Django-rendered frontend with a fully decoupled React application
- Adding proper production configuration and environment management
- Expanding the review workflow with image uploads and moderation
- Adding tests for the Django views, React components, and API services
- Improving deployment automation and CI/CD pipelines
