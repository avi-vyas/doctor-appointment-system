# Doctor Appointment System API

![Python](https://img.shields.io/badge/Python-3.9+-blue?logo=python&logoColor=white) ![FastAPI](https://img.shields.io/badge/FastAPI-0.104.1-009688?logo=fastapi&logoColor=white) ![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-2.0-blueviolet?logo=sqlalchemy&logoColor=white) ![MySQL](https://img.shields.io/badge/MySQL-DB-4479A1?logo=mysql&logoColor=white) ![JWT](https://img.shields.io/badge/Authentication-JWT-orange?logo=json-web-tokens&logoColor=white)

A robust and secure Doctor Appointment System API built with FastAPI, designed to facilitate efficient appointment management for both doctors and patients. This system provides a clear separation of concerns with role-based access control (RBAC) for `DOCTOR` and `PATIENT` users.

## Table of Contents

-   [Features](#-features)
-   [Tech Stack](#-tech-stack)
-   [Installation](#-installation)
    -   Database Setup
    -   Dependency Installation
    -   Running the API
-   [Usage & Example Flow](#-usage--example-flow)
-   [API Reference](#-api-reference)
    -   Authentication Endpoints
    -   Doctor Endpoints
    -   Patient Endpoints
-   [Project Structure](#-project-structure)
-   [Contributing](#-contributing)
-   [Footer](#%EF%B8%8F-footer)

## Features

This API provides comprehensive features for managing doctor appointments:

-   **User Authentication & Authorization**: Secure registration and login using JWT (JSON Web Tokens) with role-based access control (`DOCTOR` and `PATIENT` roles).
-   **Doctor Availability Management**: Doctors can set their working availability windows.
-   **Patient Doctor Listing**: Patients can view a list of all registered doctors.
-   **Detailed Availability Viewing**: Patients can check 30-minute availability slots for specific doctors.
-   **Appointment Booking**: Patients can book available slots with a chosen doctor.
-   **Appointment Cancellation**: Patients have the ability to cancel their own booked appointments.
-   **Appointment Listing**: Doctors can list all appointments booked with them, and patients can list their own booked appointments.
-   **Password Hashing**: User passwords are securely stored using bcrypt.
-   **Automatic Table Creation**: Database tables are automatically created on API startup.

## Tech Stack

The `doctor-appointment-system` API is built using the following technologies:

-   **Backend Framework**: FastAPI 
-   **Programming Language**: Python 
-   **Asynchronous Web Server**: Uvicorn
-   **Database**: MySQL 
-   **ORM**: SQLAlchemy (async support with `aiomysql`)
-   **Authentication**: Passlib for password hashing, python-jose for JWT handling
-   **Data Validation**: Pydantic for request and response schemas

## Installation

Follow these steps to set up and run the Doctor Appointment System API locally.

### Database Setup

1.  **Ensure MySQL is Running**: Make sure your MySQL server instance is active.
2.  **Create Database**: Create a database named `doctor_appointment_db` in your MySQL server.
    ```sql
    CREATE DATABASE doctor_appointment_db;
    ```
3.  **Configure Database Credentials**: Open `doctor_appointment_api/config.py` and update the `DB_URL` with your MySQL credentials if they differ from the default.
    ```python
    DB_URL = "mysql+aiomysql://root:your_password@127.0.0.1:3306/doctor_appointment_db"
    ```

### Dependency Installation

Navigate to the project root directory (one level above the `doctor_appointment_api` folder) and install the required Python packages:

```bash
cd /path/to/project/root
pip install --upgrade pip
pip install fastapi uvicorn sqlalchemy passlib python-jose aiomysql pydantic
```

### Running the API

From the same project root directory, run the FastAPI application using Uvicorn:

```bash
uvicorn doctor_appointment_api.main:app --reload
```

The API will be accessible at `http://127.0.0.1:8000`.

-   **Swagger UI**: Access the interactive API documentation at `http://127.0.0.1:8000/docs` .
-   **OpenAPI JSON**: View the OpenAPI specification at `http://127.0.0.1:8000/openapi.json`.

**Note**: On the first run, the necessary database tables (`users`, `availability`, `appointments`) will be automatically created.

## Usage & Example Flow

Here's a step-by-step example of how to use the Doctor Appointment System API, demonstrating a typical doctor and patient interaction flow.

### Example Flow

1.  **Register Users**: Register a doctor and a patient using the `POST /auth/register` endpoint.

    *   **Doctor Registration Example**:
        ```bash
        curl -X POST "http://127.0.0.1:8000/auth/register" \
             -H "Content-Type: application/json" \
             -d '{ "email": "doctor@example.com", "password": "doctorpass", "role": "DOCTOR", "name": "Dr. Smith" }'
        ```
    *   **Patient Registration Example**:
        ```bash
        curl -X POST "http://127.0.0.1:8000/auth/register" \
             -H "Content-Type: application/json" \
             -d '{ "email": "patient@example.com", "password": "patientpass", "role": "PATIENT", "name": "Alice" }'
        ```

2.  **User Login**: Log in as the doctor or patient to obtain an `access_token`.

    *   **Doctor Login Example**:
        ```bash
        curl -X POST "http://127.0.0.1:8000/auth/login" \
             -H "Content-Type: application/x-www-form-urlencoded" \
             -d "username=doctor@example.com&password=doctorpass"
        # Output will include {"access_token": "...", "token_type": "bearer"}
        ```

3.  **Doctor Sets Availability**: The doctor logs in and sets their availability window.

    *   **Set Availability Example** (using doctor's `access_token`):
        ```bash
        curl -X POST "http://127.0.0.1:8000/doctors/availability" \
             -H "Authorization: Bearer <doctor_access_token>" \
             -H "Content-Type: application/json" \
             -d '{ "start_datetime": "2024-08-01T09:00:00", "end_datetime": "2024-08-01T17:00:00" }'
        ```

4.  **Patient Views Doctors & Availability**: The patient logs in, lists doctors, and then checks a specific doctor's availability slots.

    *   **List Doctors Example** (using patient's `access_token`):
        ```bash
        curl -X GET "http://127.0.0.1:8000/doctors" \
             -H "Authorization: Bearer <patient_access_token>"
        ```
    *   **View Doctor's Availability Example** (assuming `doctor_id` is 1 and using patient's `access_token`):
        ```bash
        curl -X GET "http://127.0.0.1:8000/doctors/1/availability" \
             -H "Authorization: Bearer <patient_access_token>"
        ```

5.  **Patient Books an Appointment**: The patient books an available slot with a doctor.

    *   **Book Appointment Example** (assuming `doctor_id` is 1 and using patient's `access_token`):
        ```bash
        curl -X POST "http://127.0.0.1:8000/appointment/1/book" \
             -H "Authorization: Bearer <patient_access_token>" \
             -H "Content-Type: application/json" \
             -d '{ "doctor_id": 1, "start_time": "2024-08-01T09:00:00" }'
        ```

6.  **Doctor Views Bookings**: The doctor checks their list of booked appointments.

    *   **View Doctor's Appointments Example** (using doctor's `access_token`):
        ```bash
        curl -X GET "http://127.0.0.1:8000/appointments" \
             -H "Authorization: Bearer <doctor_access_token>"
        ```

7.  **Patient Views/Cancels Appointments**: The patient can view their own appointments or cancel an existing one.

    *   **View Patient's Appointments Example** (using patient's `access_token`):
        ```bash
        curl -X GET "http://127.0.0.1:8000/appointments/my" \
             -H "Authorization: Bearer <patient_access_token>"
        ```
    *   **Cancel Appointment Example** (assuming `appointment_id` is 1 and using patient's `access_token`):
        ```bash
        curl -X POST "http://127.0.0.1:8000/appointment/1/cancel" \
             -H "Authorization: Bearer <patient_access_token>"
        ```

## API Reference

All API endpoints are prefixed with `/` unless otherwise specified and are secured with JWT authentication and RBAC where noted.

| HTTP Method | Endpoint                                   | Description                                   | Required Role |
| :---------- | :----------------------------------------- | :-------------------------------------------- | :------------ |
| `POST`      | `/auth/register`                           | Create a new user (Doctor or Patient).        | Open          |
| `POST`      | `/auth/login`                              | Authenticate user, returns JWT.               | Open          |
| `POST`      | `/auth/forgot-password`                    | Mock password reset functionality.            | Open          |
| `POST`      | `/doctors/availability`                    | Set a doctor's working availability window.   | `DOCTOR`      |
| `GET`       | `/appointments`                            | List all appointments booked with the doctor. | `DOCTOR`      |
| `GET`       | `/doctors`                                 | List all registered doctors.                  | `PATIENT`     |
| `GET`       | `/doctors/{doctor_id}/availability`        | View 30-minute availability slots for a doctor. | `PATIENT`     |
| `POST`      | `/appointment/{doctor_id}/book`            | Book an appointment slot with a doctor.       | `PATIENT`     |
| `GET`       | `/appointments/my`                         | List all appointments booked by the patient.  | `PATIENT`     |
| `POST`      | `/appointment/{appointment_id}/cancel`     | Cancel a patient's own booked appointment.    | `PATIENT`     |

## Project Structure

The repository is organized into distinct directories, with the core application residing in `doctor_appointment_api/`.

```
doctor-appointment-system/
├── doctor_appointment_api/
│   ├── __init__.py
│   ├── auth.py                  # Authentication utilities (hashing, JWT, RBAC)
│   ├── config.py                # Configuration settings (DB_URL, SECRET_KEY, etc.)
│   ├── database.py              # SQLAlchemy async engine, session management
│   ├── main.py                  # FastAPI application entry point
│   ├── models.py                # SQLAlchemy ORM models (User, Availability, Appointment)
│   ├── README.md                # Specific README for the API
│   ├── routers/
│   │   ├── __init__.py
│   │   ├── auth.py              # User authentication endpoints
│   │   ├── doctor.py            # Doctor-specific endpoints
│   │   └── patient.py           # Patient-specific endpoints
│   └── schemas.py               # Pydantic data models for requests/responses
├── README.md                    # Main project README (this file)
├── week_3/                      # (Likely) Separate learning exercises or older project version
│   ├── api.py
│   ├── database.py
│   ├── main.py
│   ├── models.py
│   └── schemas.py
├── week1_answers.py             # Python programming exercises
└── week2_answers.py             # More Python programming exercises
```

**Note**: The `week_3/`, `week1_answers.py`, and `week2_answers.py` files are educational exercises and are not directly part of the `doctor-appointment-system` API application.


