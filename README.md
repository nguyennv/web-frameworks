# Which is the fastest?

[![Build Status](https://travis-ci.com/the-benchmarker/web-frameworks.svg?branch=master)](https://travis-ci.com/the-benchmarker/web-frameworks)
[![Join the chat at https://gitter.im/which_is_the_fastest/Lobby](https://badges.gitter.im/which_is_the_fastest/Lobby.svg)](https://gitter.im/which_is_the_fastest/Lobby)

This project aims to be a load benchmarking suite, no more, no less

> Measuring response times (routing times) for each framework (middleware).


<div align="center">
  :warning::warning::warning::warning::warning::warning::warning::warning:
</div>

<div align="center">Results are not <b>production-ready</b> <i>yet</i></div>

<div align="center">
  :warning::warning::warning::warning::warning::warning::warning::warning:
</div>

### Additional purposes :

+ Helping decide between languages, depending on use case
+ Learning languages, best practices, devops culture ...
+ Having fun :heart:

## Requirements

+ [Crystal](https://crystal-lang.org) as `built-in` tools are made in this language
+ [Docker](https://www.docker.com) as **frameworks** are `isolated` into _containers_
+ [wrk](https://github.com/wg/wrk) as benchmarking tool, `>= 4.1.0`
+ [postgresql](https://www.postgresql.org) to store data, `>= 10`

:information_source: you need `wrk` **stable**

~~~sh
git clone --branch 4.1.0 https://github.com/wg/wrk
~~~

:warning: `docker` is used for **development** purpose, `production` results will be computed on [DigitalOcean](https://www.digitalocean.com) :warning:

## Usage

+ Install all dependencies

~~~sh
shards install
~~~

+ Build internal tools

~~~sh
shards build
~~~

+ Create and initialize the database

~~~sh
createdb -U postgres benchmark
psql -U postgres -d benchmark < .ci/dump.sql
~~~

Docker can be used to set up the database:

~~~sh
docker run -it --rm -d \
  -p 5432:5432 \
  -e POSTGRES_DB=benchmark \
  -e POSTGRES_HOST_AUTH_METHOD=trust \
  -v /tmp/pg-data:/var/lib/postgresql/data \
  --name pg postgres:12-alpine
~~~

Wait several seconds for the container to start, then inject the dump:

~~~sh
docker exec pg sh -c "echo \"$(cat .ci/dump.sql)\" | psql -U postgres -d benchmark"
~~~

After creating the database, export its URL:

~~~sh
export DATABASE_URL="postgresql://postgres@localhost/benchmark"
~~~

+ Make configuration

~~~sh
bin/make config
~~~

+ Build containers

> jobs are either languages (example : crystal) or frameworks (example : router.cr)

~~~sh
bin/neph [job1] [job2] [job3] ...
~~~

+ Export all results readme

~~~sh
bin/db to_readme
~~~

## Results

:information_source:  Updated on **2020-04-08** :information_source:

> Benchmarking with [wrk](https://github.com/ioquatix/wrk)
   + Threads : 8
   + Timeout : 8
   + Duration : 15s (seconds)

:information_source: Sorted by max `req/s` on concurrency **64** :information_source:

|    | Language | Framework | Speed (64) | Speed (256) | Speed (512) | Speed (1024) |  Speed (2048) |
|----|----------|-----------|-----------:|------------:|------------:|-------------:|--------------:|
| 1 | go (1.14)| [gorilla-mux](https://www.gorillatoolkit.org/pkg/mux) (1.7) | 58 010 | 59 381 | 60 096 | 59 889 | 60 702 |
| 2 | go (1.14)| [rte](https://github.com/jwilner/rte) (0.0) | 48 558 | 43 234 | 42 187 | 40 853 | 41 426 |
| 3 | go (1.14)| [kami](https://github.com/guregu/kami) (2.2) | 46 051 | 37 903 | 38 310 | 38 295 | 38 468 |
| 4 | go (1.14)| [violetear](https://violetear.org) (7.0) | 39 484 | 30 069 | 28 186 | 27 309 | 27 017 |
| 5 | go (1.14)| [gorouter](https://github.com/vardius/gorouter/wiki) (4.4) | 39 291 | 36 544 | 37 139 | 37 171 | 36 528 |
| 6 | go (1.14)| [gf](https://goframe.org) (1.12) | 37 835 | 30 887 | 29 748 | 28 359 | 27 897 |
| 7 | go (1.14)| [goroute](https://goroute.github.io) (0.0) | 36 811 | 29 478 | 28 337 | 29 267 | 28 238 |
| 8 | go (1.14)| [aero](https://github.com/aerogo/aero) (1.3) | 32 194 | 27 940 | 27 871 | 27 274 | 27 271 |
| 9 | go (1.14)| [router](https://pkg.go.dev/github.com/fasthttp/router) (1.0) | 30 477 | 26 963 | 26 741 | 26 501 | 26 974 |
| 10 | go (1.14)| [gin](https://gin-gonic.com) (1.6) | 30 055 | 26 055 | 25 777 | 24 873 | 24 156 |
| 11 | go (1.14)| [webgo](https://github.com/bnkamalesh/webgo) (3.0) | 29 947 | 24 768 | 23 471 | 22 251 | 22 477 |
| 12 | go (1.14)| [fasthttp](https://pkg.go.dev/github.com/valyala/fasthttp) (1.9) | 29 802 | 26 921 | 27 353 | 26 739 | 27 051 |
| 13 | go (1.14)| [mars](https://github.com/roblillack/mars) (1.0) | 29 340 | 28 317 | 28 224 | 26 966 | 28 461 |
| 14 | go (1.14)| [fasthttprouter](https://pkg.go.dev/github.com/buaazp/fasthttprouter) (0.1) | 28 717 | 25 828 | 25 919 | 26 262 | 25 595 |
| 15 | go (1.14)| [echo](https://echo.labstack.com) (4.1) | 28 694 | 25 005 | 23 979 | 22 892 | 23 567 |
| 16 | go (1.14)| [httprouter](https://pkg.go.dev/github.com/julienschmidt/httprouter) (1.3) | 28 424 | 27 457 | 26 198 | 23 909 | 25 006 |
| 17 | go (1.14)| [air](https://github.com/aofei/air) (0.15) | 27 899 | 25 630 | 25 843 | 25 213 | 25 481 |
| 18 | go (1.14)| [chi](https://github.com/go-chi/chi) (4.1) | 27 543 | 31 807 | 31 322 | 31 141 | 31 142 |
| 19 | go (1.14)| [fiber](https://fiber.wiki) (1.8) | 27 040 | 23 741 | 24 383 | 25 302 | 25 373 |
| 20 | go (1.14)| [beego](https://beego.me) (1.12) | 26 473 | 22 959 | 22 035 | 21 121 | 21 044 |
| 21 | go (1.14)| [atreugo](https://github.com/savsgio/atreugo/blob/master/docs/README.md) (11.0) | 26 291 | 22 402 | 22 870 | 23 721 | 24 255 |
| 22 | go (1.14)| [gorouter-fasthttp](https://github.com/vardius/gorouter/wiki) (4.4) | 25 563 | 24 200 | 24 920 | 24 366 | 23 523 |
| 23 | go (1.14)| [gramework](https://github.com/gramework/gramework) (1.7) | 22 537 | 24 429 | 24 693 | 25 480 | 25 215 |

## How to contribute ?

In any way you want ...

+ Request a framework addition
+ Report a bug (on any implementation)
+ Suggest an idea
+ ...

Any kind of idea is :heart:

## Contributors

- [Taichiro Suzuki](https://github.com/tbrand) - Author | Maintainer
- [OvermindDL1](https://github.com/OvermindDL1) - Maintainer
- [Marwan Rabbâa](https://github.com/waghanza) - Maintainer
