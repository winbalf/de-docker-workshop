# de-docker-workshop
Workshop docker codespaces

<!-- config bash -->
PS1="> "
echo 'PS1="> "' > ~/.bashrc

<!-- stopped docker containers -->
docker ps -a

<!-- delete stopped docker containers -->
docker rm `docker ps -aq`

docker run -it --rm -v $(pwd)/test:/app/test --entrypoint=bash python:3.13.11-slim


pip install uv
uv init --python=3.13
uv add pandas pyarrow