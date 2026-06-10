# hiber9dev2

A Java learning project that models a school domain with **Jakarta Persistence** and **Hibernate 6**, backed by **MySQL**.

## Overview

The project defines JPA entities for teachers, courses, regions, and supplementary teacher details. Hibernate maps these entities to a relational schema and can update the database automatically on startup (`hbm2ddl.auto=update`).

## Tech stack

| Component | Version |
|-----------|---------|
| Java | 21 |
| Hibernate ORM | 6.5.2.Final |
| Jakarta Persistence | 3.1 |
| MySQL Connector/J | 9.5.0 |
| Lombok | 1.18.42 |
| Maven | 3.x |

## Domain model

```
Region 1 ── * Teacher * ── * Course
                │
                └── 1 TeacherMoreInfo
```

| Entity | Table | Description |
|--------|-------|-------------|
| `Region` | `regions` | Geographic or organizational region |
| `Teacher` | `teachers` | Teacher profile, linked to a region |
| `Course` | `courses` | Course with title, comments, and lesson type |
| `TeacherMoreInfo` | `teacher_more_info` | Date of birth and gender for a teacher |

### Relationships

- **Region → Teacher** — one-to-many (`Teacher.region`)
- **Teacher ↔ Course** — many-to-many via `courses_teachers` (owned by `Course`)
- **Teacher → TeacherMoreInfo** — one-to-one with cascade and orphan removal

Bidirectional associations are maintained through helper methods such as `Region.addTeacher()`, `Course.addTeacher()`, and `Teacher.addCourse()`.

### Enums

- `GenderType` — `MALE`, `FEMALE`, `OTHER` (stored as `STRING`)
- `LessonType` — `THEORY`, `LAB`, `MIXED` (stored as `ORDINAL`)

## Prerequisites

- JDK 21
- Maven 3.8+
- MySQL 8.x running locally

## Database setup

1. Create the database:

```sql
CREATE DATABASE hiber9dev2 CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

2. Configure connection settings in `src/main/resources/META-INF/persistence.xml`:

   - JDBC URL (default: `jdbc:mysql://localhost:3306/hiber9dev2?serverTimezone=UTC`)
   - Username
   - Password

   Update the `hibernate.connection.username` and `hibernate.connection.password` properties for your local MySQL user. Avoid committing real credentials to version control.

3. On first run, Hibernate creates or updates tables automatically (`hibernate.hbm2ddl.auto=update`).

## Build and run

Compile the project:

```bash
mvn clean compile
```

Package a JAR:

```bash
mvn clean package
```

Run tests:

```bash
mvn test
```

Run the main class from your IDE or:

```bash
mvn exec:java -Dexec.mainClass="gr.aueb.cf.App"
```

> **Note:** `App` is currently a placeholder entry point. Persistence is configured via the `schoolPU` persistence unit in `persistence.xml`.

## Project structure

```
src/main/java/gr/aueb/cf/
├── App.java                 # Application entry point
├── enums/
│   ├── GenderType.java
│   └── LessonType.java
└── model/
    ├── Course.java
    ├── Region.java
    ├── Teacher.java
    └── TeacherMoreInfo.java

src/main/resources/META-INF/
└── persistence.xml          # JPA / Hibernate configuration

src/test/java/gr/aueb/cf/
└── AppTest.java
```

## Persistence configuration

- **Persistence unit:** `schoolPU`
- **Transaction type:** `RESOURCE_LOCAL`
- **Provider:** `org.hibernate.jpa.HibernatePersistenceProvider`
- **SQL logging:** enabled (`hibernate.show_sql`, `hibernate.format_sql`)

Example `EntityManager` bootstrap:

```java
EntityManagerFactory emf = Persistence.createEntityManagerFactory("schoolPU");
EntityManager em = emf.createEntityManager();
```

Always close the `EntityManager` and `EntityManagerFactory` when finished.

## IDE setup

1. Import the project as a Maven project.
2. Enable annotation processing for Lombok (IntelliJ: *Settings → Build → Compiler → Annotation Processors*).
3. Install the Lombok plugin if your IDE requires it.

## License

Educational project — no license specified.
