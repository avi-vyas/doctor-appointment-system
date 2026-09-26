# Doctor Appointment System

Appointment management API built with **Python and FastAPI** for doctors and patients.

## Features

* **JWT authentication and RBAC** for doctor and patient workflows.
* Doctor **availability management** with configurable availability windows.
* Automatic generation of **30-minute appointment slots**.
* Patient workflow for **browsing doctors and booking appointments**.
* **Conflict detection** to prevent overlapping appointments.
* Appointment **booking, cancellation, and listing** for doctors and patients.
* **BCrypt password hashing** for secure credential storage.
* **Async database access** using SQLAlchemy with MySQL/aiomysql.
* **Pydantic** models for request/response validation.
* Modular **API and service architecture**.
* Automatic database table creation.

## Tech Stack

**Python · FastAPI · Uvicorn · SQLAlchemy Async · MySQL · aiomysql · JWT · python-jose · Passlib · BCrypt · Pydantic**
