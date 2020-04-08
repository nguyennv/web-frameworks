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
| 1 | crystal (0.33)| [lucky](https://luckyframework.org) (0.19) | 66 992 | 66 531 | 66 596 | 68 549 | 68 815 |
| 2 | crystal (0.33)| [grip](https://github.com/grip-framework/grip) (0.28) | 62 100 | 60 188 | 60 809 | 59 555 | 61 093 |
| 3 | go (1.14)| [gorilla-mux](https://www.gorillatoolkit.org/pkg/mux) (1.7) | 58 010 | 59 381 | 60 096 | 59 889 | 60 702 |
| 4 | go (1.14)| [rte](https://github.com/jwilner/rte) (0.0) | 48 558 | 43 234 | 42 187 | 40 853 | 41 426 |
| 5 | crystal (0.33)| [kemal](https://kemalcr.com) (0.26) | 46 450 | 41 434 | 41 506 | 39 390 | 42 704 |
| 6 | crystal (0.33)| [toro](https://github.com/soveran/toro) (0.4) | 45 883 | 44 032 | 43 171 | 42 035 | 41 685 |
| 7 | crystal (0.33)| [orion](https://github.com/obsidian/orion) (2.3) | 45 630 | 44 872 | 45 200 | 45 727 | 42 473 |
| 8 | crystal (0.33)| [onyx](https://onyxframework.org) (0.5) | 45 362 | 41 502 | 42 215 | 41 117 | 40 140 |
| 9 | go (1.14)| [kami](https://github.com/guregu/kami) (2.2) | 40 609 | 34 400 | 33 853 | 33 899 | 34 459 |
| 10 | go (1.14)| [chi](https://github.com/go-chi/chi) (4.1) | 39 569 | 31 807 | 31 322 | 31 141 | 31 142 |
| 11 | go (1.14)| [violetear](https://violetear.org) (7.0) | 39 484 | 30 069 | 28 186 | 27 309 | 27 017 |
| 12 | crystal (0.33)| [athena](https://github.com/athena-framework/athena) (0.8) | 38 433 | 40 654 | 37 999 | 37 166 | 37 465 |
| 13 | go (1.14)| [gorouter](https://github.com/vardius/gorouter/wiki) (4.4) | 37 329 | 33 892 | 34 555 | 34 410 | 34 034 |
| 14 | crystal (0.33)| [ricr](https://ricr-web.github.io/ricr) (0.1) | 37 136 | 31 555 | 31 834 | 30 785 | 30 540 |
| 15 | go (1.14)| [goroute](https://goroute.github.io) (0.0) | 36 811 | 29 478 | 28 337 | 29 267 | 28 238 |
| 16 | go (1.14)| [gf](https://goframe.org) (1.12) | 36 461 | 30 465 | 29 246 | 28 071 | 27 941 |
| 17 | crystal (0.33)| [router.cr](https://github.com/tbrand/router.cr) (0.2) | 36 328 | 30 569 | 29 422 | 28 207 | 27 514 |
| 18 | go (1.14)| [fasthttprouter](https://pkg.go.dev/github.com/buaazp/fasthttprouter) (0.1) | 34 130 | 30 660 | 30 767 | 31 622 | 31 110 |
| 19 | go (1.14)| [httprouter](https://pkg.go.dev/github.com/julienschmidt/httprouter) (1.3) | 32 678 | 31 220 | 30 815 | 29 230 | 29 582 |
| 20 | go (1.14)| [router](https://pkg.go.dev/github.com/fasthttp/router) (1.0) | 32 509 | 28 796 | 28 216 | 27 705 | 28 204 |
| 21 | go (1.14)| [aero](https://github.com/aerogo/aero) (1.3) | 32 194 | 27 940 | 27 871 | 27 274 | 27 271 |
| 22 | go (1.14)| [fasthttp](https://pkg.go.dev/github.com/valyala/fasthttp) (1.9) | 31 460 | 28 399 | 28 420 | 28 250 | 28 554 |
| 23 | go (1.14)| [fiber](https://fiber.wiki) (1.8) | 31 317 | 28 658 | 29 180 | 29 865 | 30 469 |
| 24 | crystal (0.33)| [spider-gazelle](https://spider-gazelle.net) (2.3) | 30 808 | 29 158 | 29 677 | 29 933 | 29 632 |
| 25 | go (1.14)| [beego](https://beego.me) (1.12) | 30 431 | 27 153 | 26 715 | 25 760 | 23 678 |
| 26 | go (1.14)| [gin](https://gin-gonic.com) (1.6) | 30 055 | 26 055 | 25 777 | 24 873 | 24 156 |
| 27 | go (1.14)| [webgo](https://github.com/bnkamalesh/webgo) (3.0) | 29 947 | 24 768 | 23 471 | 22 251 | 22 477 |
| 28 | go (1.14)| [atreugo](https://github.com/savsgio/atreugo/blob/master/docs/README.md) (11.0) | 29 742 | 25 763 | 25 915 | 26 186 | 26 395 |
| 29 | go (1.14)| [mars](https://github.com/roblillack/mars) (1.0) | 29 683 | 28 544 | 28 384 | 27 356 | 28 247 |
| 30 | go (1.14)| [echo](https://echo.labstack.com) (4.1) | 28 694 | 25 005 | 23 979 | 22 892 | 23 567 |
| 31 | go (1.14)| [air](https://github.com/aofei/air) (0.15) | 27 899 | 25 630 | 25 843 | 25 213 | 25 481 |
| 32 | go (1.14)| [gorouter-fasthttp](https://github.com/vardius/gorouter/wiki) (4.4) | 25 563 | 24 200 | 24 920 | 24 366 | 23 523 |
| 33 | go (1.14)| [gramework](https://github.com/gramework/gramework) (1.7) | 24 550 | 26 876 | 27 085 | 27 579 | 27 291 |
| 34 | crystal (0.33)| [amber](https://amberframework.org) (0.33) | 19 584 | 18 614 | 17 802 | 17 000 | 16 657 |

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
