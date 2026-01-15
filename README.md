# de-docker-workshop
Workshop docker codespaces

### config bash
PS1="> "
echo 'PS1="> "' > ~/.bashrc

### stopped docker containers
docker ps -a

### delete stopped docker containers
docker rm `docker ps -aq`

docker run -it --rm -v $(pwd)/test:/app/test --entrypoint=bash python:3.13.11-slim


pip install uv
uv init --python=3.13
uv add pandas pyarrow


### Dockerfile
  FROM: Base image (Python 3.13)
  RUN: Execute commands during build
  WORKDIR: Set working directory
  COPY: Copy files into the image
  ENTRYPOINT: Default command to run

### Jupyter
uv add --dev jupyter
uv run jupyter notebook

### Running PostgreSQL with Docker
docker run -it --rm \
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v ny_taxi_postgres_data:/var/lib/postgresql \
  -p 55432:5432 \
  postgres:18

### pgcli
uv add --dev pgcli
uv run pgcli -h localhost -p 55432 -u root -d ny_taxi

### convert jupyter notebook to script
uv run jupyter nbconvert --to=script notebook.ipynb

### create docker network
docker network create pg-network

# Run PostgreSQL on the network
docker run -it \
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v ny_taxi_postgres_data:/var/lib/postgresql \
  -p 5432:5432 \
  --network=pg-network \
  --name pgdatabase \
  postgres:18

### Run the Containerized Ingestion
docker run -it \
  --network=pg-network \
  taxi_ingest:v001 \
    --pg-user=root \
    --pg-pass=root \
    --pg-host=pgdatabase \
    --pg-port=5432 \
    --pg-db=ny_taxi \
    --target-table=yellow_taxi_trips_2021_2 \
    --year=2021 \
    --month=2 \
    --chunksize=100000

# In another terminal, run pgAdmin on the same network
docker run -it \
  -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
  -e PGADMIN_DEFAULT_PASSWORD="root" \
  -v pgadmin_data:/var/lib/pgadmin \
  -p 8085:80 \
  --network=pg-network \
  --name pgadmin \
  dpage/pgadmin4

### Start Services with Docker Compose
docker-compose up

If you want to re-run the dockerized ingest script when you run Postgres and pgAdmin with docker-compose, you will have to find the name of the virtual network that Docker compose created for the containers. You can use the command docker network ls to find it and then change the docker run command for the dockerized script to include the network name.

-- check the network link:

docker network ls 

it's pipeline_default, now run the script:

docker run -it \
  --network=pipeline_default \
  taxi_ingest:v001 \
    --pg-user=root \
    --pg-pass=root \
    --pg-host=pgdatabase \
    --pg-port=5432 \
    --pg-db=ny_taxi \
    --target-table=yellow_taxi_trips_2021_2 \
    --year=2021 \
    --month=2 \
    --chunksize=100000