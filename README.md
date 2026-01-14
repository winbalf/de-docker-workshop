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