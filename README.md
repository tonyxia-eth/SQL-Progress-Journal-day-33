# SQL-Progress-Journal-day-33

# LinkedIn Database Schema Project

## Overview
This project is a simplified database schema for a LinkedIn-like platform built using SQLite. It models users, schools, companies, and the connections between them, including user affiliations with schools and companies.

## Database Schema

### Tables:
- **users**: Stores user information (first name, last name, username, password)
- **schools**: Stores school details (name, type, location, founding year)
- **companies**: Stores company details (name, industry, location)
- **user_school_affiliations**: Links users to schools with start/end dates and degrees
- **user_company_affiliations**: Links users to companies with start/end dates and job titles
- **user_connections**: Represents mutual connections between users (enforced with a check constraint)

## Sample Data
- Users: Alan Garber, Reid Hoffman
- School: Harvard University (University, Cambridge, MA, founded 1636)
- Company: LinkedIn (Technology, Sunnyvale, CA)
- Alan Garber’s BA at Harvard (1973–1976)
- Reid Hoffman’s role as CEO and Chairman at LinkedIn (2003–2007)
- Connection between Alan and Reid

comands used:

CREATE TABLE "users" (
    "id" INTEGER PRIMARY KEY,
    "first_name" TEXT NOT NULL,
    "last_name" TEXT NOT NULL,
    "username" TEXT NOT NULL,
    "password" TEXT NOT NULL
);

CREATE TABLE "schools" (
    "id" INTEGER PRIMARY KEY,
    "name" TEXT NOT NULL,
    "type" TEXT NOT NULL,
    "location" TEXT NOT NULL,
    "year_founded" INTEGER
);

CREATE TABLE "companies" (
    "id" INTEGER PRIMARY KEY,
    "name" TEXT NOT NULL,
    "industry" TEXT NOT NULL,
    "location" TEXT NOT NULL
);

CREATE TABLE "user_school_affiliations" (
    "id" INTEGER PRIMARY KEY,
    "user_id" INTEGER NOT NULL,
    "school_id" INTEGER NOT NULL,
    "start_date" TEXT NOT NULL,
    "end_date" TEXT,  -- nullable if ongoing
    "degree" TEXT,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (school_id) REFERENCES schools(id)
);

CREATE TABLE "user_company_affiliations" (
    "id" INTEGER PRIMARY KEY,
    "user_id" INTEGER NOT NULL,
    "company_id" INTEGER NOT NULL,
    "start_date" TEXT NOT NULL,
    "end_date" TEXT,  -- nullable if ongoing
    "title" TEXT,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (company_id) REFERENCES companies(id)
);

CREATE TABLE "user_connections" (
    "id" INTEGER PRIMARY KEY,
    "user1_id" INTEGER NOT NULL,
    "user2_id" INTEGER NOT NULL,
    FOREIGN KEY (user1_id) REFERENCES users(id),
    FOREIGN KEY (user2_id) REFERENCES users(id),
    CHECK (user1_id < user2_id) -- enforce one direction only
);

INSERT INTO users (first_name, last_name, username, password)
VALUES
  ('Alan', 'Garber', 'alan', 'password'),
  ('Reid', 'Hoffman', 'reid', 'password');

INSERT INTO schools (name, type, location, year_founded)
VALUES ('Harvard University', 'University', 'Cambridge, Massachusetts', 1636);

INSERT INTO companies (name, industry, location)
VALUES ('LinkedIn', 'Technology', 'Sunnyvale, California');

INSERT INTO user_school_affiliations (user_id, school_id, start_date, end_date, degree)
VALUES (
  (SELECT id FROM users WHERE username = 'alan'),
  (SELECT id FROM schools WHERE name = 'Harvard University'),
  '1973-09-01',
  '1976-06-01',
  'BA'
);

INSERT INTO user_company_affiliations (user_id, company_id, start_date, end_date, title)
VALUES (
  (SELECT id FROM users WHERE username = 'reid'),
  (SELECT id FROM companies WHERE name = 'LinkedIn'),
  '2003-01-01',
  '2007-02-01',
  'CEO and Chairman'
);

INSERT INTO user_connections (user1_id, user2_id)
VALUES (
  (SELECT id FROM users WHERE username = 'alan'),
  (SELECT id FROM users WHERE username = 'reid')
);


