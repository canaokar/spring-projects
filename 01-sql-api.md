SPRING BOOT SQL + API ASSIGNMENT
================================

Objective
---------
Build a Spring Boot application that integrates:
- External REST APIs
- MySQL persistence using JPA
- Basic business rules and validation
- Simple “cron-like” behaviour via an endpoint

This lab is designed to test real backend fundamentals:
REST design, database interaction, external API calls, and clean separation of concerns.


Functional Requirements
-----------------------

The system must allow users to:

1. Fetch current weather for any city using an external Weather API
2. Save favourite cities to a MySQL database
3. Define alert temperature thresholds per city
   - Example:
     - Alert if temperature < 5°C
     - Alert if temperature > 35°C
4. Fetch current weather for ALL saved cities in a single API call
5. Automatically mark a city as being in an "ALERT" state if thresholds are crossed
6. OPTIONAL:
   Add a cron-like simulation endpoint to refresh weather data for all cities


Technology Constraints
----------------------

- Language: Java
- Framework: Spring Boot
- Database: MySQL
- ORM: Spring Data JPA (Hibernate)
- External API access using RestTemplate or WebClient
- No frontend required
- JSON only (no HTML)


High-Level Architecture
-----------------------

Controller Layer
- Exposes REST endpoints
- Accepts and validates user input
- Returns JSON responses

Service Layer
- Contains business logic
- Calls external weather API
- Applies alert logic
- Coordinates database operations

Repository Layer
- JPA repositories for MySQL access

Model Layer
- Entity classes mapped to database tables
- DTOs for API input/output


Database Design
---------------

Table: favorite_cities

Columns:
- id (BIGINT, PRIMARY KEY, AUTO_INCREMENT)
- city_name (VARCHAR, NOT NULL)
- min_temp_alert (DOUBLE, NULL)
- max_temp_alert (DOUBLE, NULL)
- last_known_temp (DOUBLE, NULL)
- alert (BOOLEAN, NOT NULL)
- last_updated (TIMESTAMP)


Core Concepts to Implement
--------------------------

1. External API Integration
   - Call a public weather API (OpenWeatherMap or similar)
   - Handle API failures gracefully
   - Parse and map JSON response to Java objects

2. Persistence with JPA
   - Use @Entity, @Id, @GeneratedValue
   - Repository interfaces extending JpaRepository
   - No raw JDBC

3. Business Logic
   - Determine alert state:
       alert = true if
       temperature < min_temp_alert OR
       temperature > max_temp_alert
   - Store last known temperature

4. Basic Validation
   - City name must not be blank
   - Alert thresholds must make logical sense
     (min < max if both provided)
   - Validation can be done in controller or service layer


Suggested REST Endpoints
------------------------

GET /weather/{city}
-------------------
Purpose:
- Fetch live weather for a city using the external API
- Does NOT save anything to the database

Response:
{
  "city": "London",
  "temperature": 18.5,
  "description": "Cloudy"
}


POST /favorites
---------------
Purpose:
- Save a city as a favourite
- Configure alert temperature thresholds

Request Body:
{
  "city": "London",
  "minTemp": 5,
  "maxTemp": 35
}

Behaviour:
- Fetch current weather
- Store city, thresholds, temperature
- Compute alert state

Response:
{
  "id": 1,
  "city": "London",
  "temperature": 18.5,
  "alert": false
}


GET /favorites
--------------
Purpose:
- Fetch all saved cities
- Return current weather and alert state for each

Behaviour:
- For each saved city:
    - Fetch live weather
    - Recompute alert state
    - Update last known temperature

Response:
[
  {
    "city": "London",
    "temperature": 18.5,
    "alert": false
  },
  {
    "city": "Delhi",
    "temperature": 41.0,
    "alert": true
  }
]


DELETE /favorites/{id}
----------------------
Purpose:
- Remove a saved city from the database

Response:
- HTTP 204 No Content


OPTIONAL: POST /sync
--------------------
Purpose:
- Simulate a cron job
- Refresh weather for all saved cities
- Update temperature and alert state in DB

Response:
{
  "syncedCities": 5,
  "timestamp": "2026-01-28T10:30:00"
}


Non-Functional Requirements
---------------------------

- Clean package structure
  - controller
  - service
  - repository
  - model
  - dto

- Proper HTTP status codes
- No business logic inside controllers
- No static/global variables
- No Maps used as fake databases


Evaluation Criteria
-------------------

- Correct use of Spring Boot annotations
- Proper JPA entity modeling
- Clear separation of concerns
- Correct alert logic
- Clean, readable code
- Graceful error handling


Expected Outcome
----------------

By the end of this lab, participants should demonstrate:
- Real-world Spring Boot REST development
- External API integration
- SQL persistence with JPA
- Backend system thinking beyond “CRUD only”
