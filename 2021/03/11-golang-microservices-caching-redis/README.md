# Caching in Go: Redis

## Requirements

1. Go 1.16
1. [`direnv`](https://mariocarrion.com/2020/11/20/golang-go-tool-direnv.html): for _sandboxing_ the required tools used by this project.
1. [PostgreSQL](#postgresql): either a remote instance, local binary or docker container.
1. [Redis](#memcached): either a remote instance, local binary or docker container.
1. [Load testing data](#load-testing-data)

## Running

Download the required packages:

```
go mod download
```

Then you can run `server.go` with the PostgreSQL and Redis configuration:

```
DATABASE_URL=postgres://user:password@localhost:5432/dbname?sslmode=disable \
REDIS=localhost:6379 \
  go run .
```

### PostgreSQL

#### Using Docker

```
docker run \
  -d \
  -e POSTGRES_HOST_AUTH_METHOD=trust \
  -e POSTGRES_USER=user \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=dbname \
  -p 5432:5432 \
  postgres:12.5-alpine
```

#### Migrations

Make sure the `migrate` tool is installed:

```
go install -tags 'postgres' github.com/golang-migrate/migrate/v4/cmd/migrate@v4.14.1
```

Then, assuming you used docker, you can run:

```
migrate -path db/migrations/ -database "postgres://user:password@localhost:5432/dbname?sslmode=disable" up
```

### Redis

```
docker run \
  -d \
  -p 6379:6379 \
  redis:6.2.1-alpine3.13
```

### Load testing data

You can use the tool I build during my [Implementing Complex Pipelines in Go](https://mariocarrion.com/2020/08/27/go-implementing-complex-pipelines-part-5.html) series, install it using:

```
go install github.com/MarioCarrion/complex-pipelines/part5
```

Then run it using:

```
DATABASE_URL="postgres://user:password@localhost:5432/dbname?sslmode=disable" part5
```

Sadly, at the moment this tool does not provide any progress feedback; but it should finish after a couple of minutes.
