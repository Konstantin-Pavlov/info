services:
  postgres:
    image: postgres:14
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin
      POSTGRES_DB: db_name
    ports:
      - "5433:5432"  # Host port 5433 forwards to container's PostgreSQL port 5432
    volumes:
      - postgres_data:/var/lib/postgresql/data

5432 - Container Port: The port inside the container where the service is running
5433 - Host Port: The port on your machine that Docker listens on.
