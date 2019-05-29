Introduction
Docker image for JasperServer Community Edition.

Building your own image
Download the latest JasperServer and put in ./resources: JasperServer CE Releases

Build a new Docker image and store it in local Docker

docker build -t jasper-reports .

Tag the local Docker image with the Docker Hub username/repo:version

docker tag jasper-reports groundworkdevelopment/jasper-reports:1.0

Push new Docker image to Docker Hub

docker push groundworkdevelopment/jasper-reports:1.0

Using with Docker Compose

docker-compose.yml:

version: '3.4'

services:
  jasperserver:
    image: groundworkdevelopment/jasper-reports:1.0
    ports:
      - "7070:8080"
      #- "8443:8443"
    depends_on:
      - pg
    env_file: .env
    environment:
      - DB_HOST=localhost
    volumes:
      - jasper_webapp:/usr/local/tomcat/webapps/jasperserver
      - jasper_license:/usr/local/share/jasperreports-ce/license
      - jasper_customization:/usr/local/share/jasperreports-ce/customization
    networks:
      - default

  pg:
    image: postgres:10-alpine
    ports:
      - "5432:5432"   # PostgreSQL
    environment:
      - LANG=en_US.UTF-8
      - PGDATABASE=gwcollagedb
      - POSTGRES_DB=gwcollagedb
    healthcheck:
      test: psql --dbname gwcollagedb --user collage -c "SELECT 1;"
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 20s
    user: postgres
    volumes:
      - pgvol:/var/lib/postgresql/data

networks:
  default:

volumes:
  pgvol:
  jasper_webapp:
  jasper_license:
  jasper_customization:
  
.env

DB_USER=postgres
DB_PASSWORD=postgres
DB_PORT=5432
DB_NAME=jasperserver
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres# jasper-server
