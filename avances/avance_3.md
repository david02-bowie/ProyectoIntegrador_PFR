# Proyecto Integrador  
## Poblamiento de Base de Datos mediante Script SQL

## Descripción del Proyecto
El presente proyecto tiene como objetivo implementar un proceso de carga de datos hacia una base de datos MySQL a partir de un conjunto de datos previamente procesados en Scala.  

Para garantizar simplicidad, reproducibilidad y compatibilidad en entornos académicos, el poblamiento de la base de datos se realiza **únicamente mediante un script SQL**, evitando dependencias directas entre la aplicación y la base de datos durante la inserción.

El programa desarrollado en Scala se encarga de:
- Leer el archivo CSV original.
- Realizar limpieza y validación de datos.
- Generar un script SQL con instrucciones `INSERT INTO`.
- Permitir que la carga se ejecute manualmente en MySQL.

De esta forma, la base de datos puede poblarse sin necesidad de ejecutar nuevamente el programa.

---

## Objetivo
Implementar un mecanismo confiable para almacenar información en MySQL utilizando scripts SQL generados automáticamente, garantizando que los datos previamente limpiados puedan ser insertados de manera segura y replicable.

---

## Herramientas Utilizadas
- Scala y SBT para procesamiento y limpieza de datos.
- MySQL Server como gestor de base de datos.
- MySQL Workbench para ejecución de scripts.
- Archivo CSV como fuente de datos (no incluido en el repositorio).

---

## Estructura del Proyecto
Proyecto/
│
├── build.sbt
├── README.md
└── src/
└── main/
└── scala/

que contiene todas las instrucciones de inserción.

---

## Creación de la Base de Datos
Antes de ejecutar el script de inserción, se debe crear la base de datos y la tabla correspondiente.

Ejecutar en MySQL:

-- =====================================================
-- 1. CREAR BASE DE DATOS
-- =====================================================
DROP DATABASE IF EXISTS practicum;
CREATE DATABASE practicum
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;

USE practicum;

-- =====================================================
-- 2. TABLA COLLECTIONS
-- =====================================================
CREATE TABLE collections (
  collection_id BIGINT PRIMARY KEY,
  name VARCHAR(255) NOT NULL
);

-- =====================================================
-- 3. TABLA MOVIE (TABLA CENTRAL)
-- =====================================================
CREATE TABLE movie (
  id_movie BIGINT PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  budget DOUBLE,
  revenue DOUBLE,
  runtime INT,
  release_data DATE,
  overview TEXT,
  original_language VARCHAR(10),
  popularity DOUBLE,
  vote_average DOUBLE,
  collection_id BIGINT,
  CONSTRAINT fk_movie_collection
    FOREIGN KEY (collection_id)
    REFERENCES collections(collection_id)
);

-- =====================================================
-- 4. TABLA GENRE
-- =====================================================
CREATE TABLE genre (
  genre_id BIGINT PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);

-- =====================================================
-- 5. RELACIÓN MOVIE - GENRE (N:M)
-- =====================================================
CREATE TABLE movie_genre (
  id_movie BIGINT,
  genre_id BIGINT,
  PRIMARY KEY (id_movie, genre_id),
  CONSTRAINT fk_mg_movie
    FOREIGN KEY (id_movie)
    REFERENCES movie(id_movie),
  CONSTRAINT fk_mg_genre
    FOREIGN KEY (genre_id)
    REFERENCES genre(genre_id)
);

-- =====================================================
-- 6. TABLA COMPANY
-- =====================================================
CREATE TABLE company (
  id_company BIGINT PRIMARY KEY,
  name_company VARCHAR(255) NOT NULL
);

-- =====================================================
-- 7. RELACIÓN MOVIE - COMPANY (N:M)
-- =====================================================
CREATE TABLE movie_company (
  id_movie BIGINT,
  id_company BIGINT,
  PRIMARY KEY (id_movie, id_company),
  CONSTRAINT fk_mc_movie
    FOREIGN KEY (id_movie)
    REFERENCES movie(id_movie),
  CONSTRAINT fk_mc_company
    FOREIGN KEY (id_company)
    REFERENCES company(id_company)
);

-- =====================================================
-- 8. TABLA LENGUAJE
-- =====================================================
CREATE TABLE lenguaje (
  lenguaje_id BIGINT AUTO_INCREMENT PRIMARY KEY,
  lenguaje_iso VARCHAR(10) NOT NULL UNIQUE
);

-- =====================================================
-- 9. RELACIÓN MOVIE - LENGUAJE (N:M)
-- =====================================================
CREATE TABLE movie_lenguaje (
  id_movie BIGINT,
  lenguaje_id BIGINT,
  PRIMARY KEY (id_movie, lenguaje_id),
  CONSTRAINT fk_ml_movie
    FOREIGN KEY (id_movie)
    REFERENCES movie(id_movie),
  CONSTRAINT fk_ml_lenguaje
    FOREIGN KEY (lenguaje_id)
    REFERENCES lenguaje(lenguaje_id)
);

-- =====================================================
-- 10. TABLA PEOPLE
-- =====================================================
CREATE TABLE people (
  people_id BIGINT PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  gender INT
);

-- =====================================================
-- 11. TABLA DEPARTMENT
-- =====================================================
CREATE TABLE department (
  department_id BIGINT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL UNIQUE
);

-- =====================================================
-- 12. TABLA ROLE
-- =====================================================
CREATE TABLE role (
  role_id BIGINT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL UNIQUE
);

-- =====================================================
-- 13. TABLA MOVIE_CAST
-- =====================================================
CREATE TABLE movie_cast (
  id_movie BIGINT,
  people_id BIGINT,
  cast_order INT,
  PRIMARY KEY (id_movie, people_id),
  CONSTRAINT fk_cast_movie
    FOREIGN KEY (id_movie)
    REFERENCES movie(id_movie),
  CONSTRAINT fk_cast_people
    FOREIGN KEY (people_id)
    REFERENCES people(people_id)
);

-- =====================================================
-- 14. TABLA MOVIE_CREW
-- =====================================================
CREATE TABLE movie_crew (
  id_movie BIGINT,
  people_id BIGINT,
  department_id BIGINT,
  role_id BIGINT,
  PRIMARY KEY (id_movie, people_id, role_id),
  CONSTRAINT fk_crew_movie
    FOREIGN KEY (id_movie)
    REFERENCES movie(id_movie),
  CONSTRAINT fk_crew_people
    FOREIGN KEY (people_id)
    REFERENCES people(people_id),
  CONSTRAINT fk_crew_department
    FOREIGN KEY (department_id)
    REFERENCES department(department_id),
  CONSTRAINT fk_crew_role
    FOREIGN KEY (role_id)
    REFERENCES role(role_id)
);
