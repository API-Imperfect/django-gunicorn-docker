##########
# BUILDER #
##########

# pull official base image
FROM python:3.9.4-slim-buster as builder

# set work directory
WORKDIR /app

# set environment variables
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

# install dependencies
# install dependencies
# -y switch ensures the build does not stop and
# ask for a user prompt
RUN apt-get update \
  #dependencies for building Python packages
  && apt-get install -y build-essential \
  # psycopg2 dependencies
  && apt-get install -y libpq-dev \
  # Translation dependencies
  && apt-get install -y gettext \
  # postgresql dependencies
  && apt-get -y install netcat gcc postgresql \
  # clean up unused files
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/*

# upgrade pip version
RUN pip install --upgrade pip

# requirements are installed here to
# ensure they are cached
COPY ./requirements.txt /app/requirements.txt

#  install requirements
RUN pip install -r requirements.txt

RUN pip wheel --no-cache-dir --no-deps --wheel-dir /app/wheels -r requirements.txt

# run entrypoint shell script.
# we shall create this shortly and explain it
COPY ./docker/local/django/entrypoint /entrypoint
# process line endings of shell scripts
# thus converting windows line endings to UNIX line endings
RUN sed -i 's/\r$//g' /entrypoint
# make the entrypoint shell script executable
RUN chmod +x /entrypoint

# run start shell script.
# we shall create this shortly and explain it
COPY ./docker/local/django/start /start
RUN sed -i 's/\r$//g' /start
RUN chmod +x /start

ENTRYPOINT [ "/entrypoint" ]