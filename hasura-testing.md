### Testing

Locally

```
# Build deps and cli
cd cli
make all-in-docker

# Copy output to the migrations dir
mv cli/_output/support-multiple-database-urls-in-cli-migrations-image-30b28266-dirty/cli-hasura-linux-amd64 ./scripts/cli-migrations


cd scripts/cli-migrations
docker build .
```

#### Copy this into an existing project using the heroku deployment method

```bash
git clone https://github.com/hasura/graphql-engine-heroku
```

Paste in

./cli-hasura-linux-amd64
./docker-entrypoint.sh

Update the Dockerfile:

```
FROM hasura/graphql-engine:v1.0.0-beta.9

# set an env var to let the cli know that
# it is running in server environment
ENV HASURA_GRAPHQL_CLI_ENVIRONMENT=server-on-docker

COPY docker-entrypoint.sh /bin/
COPY cli-hasura-linux-amd64 /bin/hasura-cli
RUN chmod +x /bin/hasura-cli

ENTRYPOINT ["docker-entrypoint.sh"]

CMD graphql-engine \
  --database-url $DATABASE_URL \
  serve \
  --server-port $PORT \
  --enable-console
```

Update the heroku.yml:

```
setup:
  addons:
    - plan: heroku-postgresql
      as: DATABASE
  config:
    HASURA_GRAPHQL_MIGRATIONS_DATABASE_ENV_VAR: DATABASE_URL
build:
  docker:
    web: Dockerfile
```

### Provision

Setup the app, with addons and the setup steps defined in the heroku.yml

heroku create heroku-migration-tester-002 --manifest


### Deploy

```
export HEROKU_GIT_REMOTE=https://git.heroku.com/heroku-migration-tester-002.git

git init && git add .
git commit -m "first commit"
git remote add heroku HEROKU_GIT_REMOTE
git push heroku master
```
