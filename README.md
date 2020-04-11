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

:information_source:  Updated on **2020-04-11** :information_source:

> Benchmarking with [wrk](https://github.com/ioquatix/wrk)
   + Threads : 8
   + Timeout : 8
   + Duration : 15s (seconds)

:information_source: Sorted by max `req/s` on concurrency **64** :information_source:

|    | Language | Framework | Speed (16) | Speed (32) | Speed (64) | Speed (128) |  Speed (256) | Speed (512) |
|----|----------|-----------|-----------:|-----------:|-----------:|------------:|-------------:|------------:|
| 1 | crystal (0.34)| [amber](https://amberframework.org) (0.33) | 67 051 | 81 810 | 85 362 | 86 082 | 85 312 | 85 559 |
| 2 | go (1.14)| [httprouter](https://pkg.go.dev/github.com/julienschmidt/httprouter) (1.3) | 40 288 | 60 616 | 72 104 | 73 864 | 74 840 | 74 017 |
| 3 | go (1.14)| [violetear](https://violetear.org) (7.0) | 43 562 | 62 586 | 69 896 | 68 920 | 67 703 | 66 759 |
| 4 | crystal (0.34)| [router.cr](https://github.com/tbrand/router.cr) (0.2) | 59 684 | 66 706 | 69 692 | 66 826 | 64 740 | 64 295 |
| 5 | go (1.14)| [fasthttp](https://pkg.go.dev/github.com/valyala/fasthttp) (1.9) | 39 490 | 58 009 | 69 173 | 75 890 | 75 167 | 71 451 |
| 6 | go (1.14)| [gin](https://gin-gonic.com) (1.6) | 44 388 | 62 737 | 68 649 | 67 182 | 66 733 | 66 449 |
| 7 | go (1.14)| [webgo](https://github.com/bnkamalesh/webgo) (3.0) | 54 346 | 64 684 | 65 675 | 65 886 | 66 066 | 64 581 |
| 8 | go (1.14)| [chi](https://github.com/go-chi/chi) (4.1) | 42 879 | 59 385 | 64 346 | 64 118 | 63 409 | 60 942 |
| 9 | crystal (0.34)| [spider-gazelle](https://spider-gazelle.net) (2.3) | 39 111 | 56 415 | 61 292 | 61 555 | 60 281 | 62 207 |
| 10 | crystal (0.34)| [toro](https://github.com/soveran/toro) (0.4) | 60 401 | 63 023 | 58 485 | 54 183 | 48 323 | 46 605 |
| 11 | crystal (0.34)| [grip](https://github.com/grip-framework/grip) (0.28) | 50 106 | 55 775 | 57 920 | 56 028 | 54 282 | 52 893 |
| 12 | go (1.14)| [echo](https://echo.labstack.com) (4.1) | 40 547 | 52 505 | 55 152 | 51 922 | 49 868 | 48 157 |
| 13 | go (1.14)| [mars](https://github.com/roblillack/mars) (1.0) | 40 830 | 51 593 | 55 152 | 52 090 | 50 755 | 49 160 |
| 14 | crystal (0.34)| [ricr](https://ricr-web.github.io/ricr) (0.1) | 38 203 | 52 763 | 53 983 | 48 755 | 47 482 | 47 233 |
| 15 | go (1.14)| [air](https://github.com/aofei/air) (0.15) | 38 870 | 49 788 | 53 700 | 54 916 | 55 749 | 52 289 |
| 16 | crystal (0.34)| [lucky](https://luckyframework.org) (0.20) | 32 641 | 47 301 | 53 625 | 51 650 | 53 381 | 51 214 |
| 17 | go (1.14)| [atreugo](https://github.com/savsgio/atreugo/blob/master/docs/README.md) (11.0) | 42 138 | 51 247 | 52 820 | 48 418 | 45 843 | 44 991 |
| 18 | go (1.14)| [gorilla-mux](https://www.gorillatoolkit.org/pkg/mux) (1.7) | 43 500 | 52 965 | 51 689 | 47 673 | 45 154 | 43 358 |
| 19 | go (1.14)| [kami](https://github.com/guregu/kami) (2.2) | 39 025 | 51 793 | 51 553 | 46 840 | 42 756 | 40 629 |
| 20 | go (1.14)| [gf](https://goframe.org) (1.12) | 44 782 | 51 037 | 50 917 | 49 730 | 47 523 | 46 689 |
| 21 | python (3.8)| [flask](https://flask.pocoo.org) (1.1) | 39 448 | 49 016 | 50 769 | 51 960 | 52 125 | 52 026 |
| 22 | go (1.14)| [beego](https://beego.me) (1.12) | 39 979 | 49 155 | 48 758 | 43 551 | 39 958 | 38 653 |
| 23 | go (1.14)| [rte](https://github.com/jwilner/rte) (0.0) | 51 304 | 53 571 | 47 833 | 42 923 | 36 568 | 34 904 |
| 24 | python (3.8)| [falcon](https://falconframework.org) (2.0) | 32 377 | 46 016 | 47 521 | 48 601 | 45 058 | 44 303 |
| 25 | crystal (0.34)| [kemal](https://kemalcr.com) (0.26) | 36 737 | 46 852 | 47 075 | 43 721 | 43 560 | 43 481 |
| 26 | python (3.8)| [apidaora](https://github.com/dutradda/apidaora) (0.14) | 27 707 | 39 646 | 45 831 | 44 673 | 42 645 | 39 894 |
| 27 | python (3.8)| [pyramid](https://trypyramid.com) (1.1) | 37 005 | 44 795 | 45 519 | 44 308 | 41 770 | 42 431 |
| 28 | python (3.8)| [masonite](https://masoniteproject.com) (2.3) | 36 107 | 42 544 | 45 290 | 46 460 | 46 510 | 46 524 |
| 29 | go (1.14)| [fiber](https://fiber.wiki) (1.8) | 39 700 | 45 789 | 44 642 | 40 018 | 36 221 | 34 825 |
| 30 | go (1.14)| [aero](https://github.com/aerogo/aero) (1.3) | 33 907 | 43 200 | 42 873 | 38 861 | 37 676 | 36 683 |
| 31 | python (3.8)| [asgineer](https://asgineer.readthedocs.io) (0.7) | 28 896 | 37 309 | 42 303 | 44 827 | 46 633 | 47 065 |
| 32 | python (3.8)| [starlette](https://starlette.io) (0.13) | 24 411 | 34 846 | 41 931 | 41 731 | 38 821 | 37 767 |
| 33 | python (3.8)| [hug](https://hug.rest) (2.6) | 32 033 | 39 635 | 41 588 | 37 934 | 35 293 | 32 991 |
| 34 | crystal (0.34)| [orion](https://github.com/obsidian/orion) (2.3) | 33 429 | 41 897 | 40 146 | 35 286 | 36 147 | 34 238 |
| 35 | go (1.14)| [goroute](https://goroute.github.io) (0.0) | 34 858 | 40 822 | 39 894 | 35 210 | 34 722 | 34 188 |
| 36 | ruby (2.7)| [agoo](https://github.com/ohler55/agoo) (2.12) | 21 613 | 32 252 | 39 553 | 39 981 | 38 945 | 37 139 |
| 37 | go (1.14)| [gramework](https://github.com/gramework/gramework) (1.7) | 30 058 | 33 043 | 39 126 | 43 944 | 45 002 | 44 780 |
| 38 | go (1.14)| [router](https://pkg.go.dev/github.com/fasthttp/router) (1.0) | 35 482 | 39 753 | 36 055 | 46 022 | 38 159 | 42 537 |
| 39 | go (1.14)| [gorouter](https://github.com/vardius/gorouter/wiki) (4.4) | 29 295 | 34 321 | 34 694 | 34 222 | 32 415 | 32 155 |
| 40 | crystal (0.34)| [athena](https://github.com/athena-framework/athena) (0.8) | 23 793 | 31 336 | 34 205 | 31 267 | 29 571 | 30 195 |
| 41 | go (1.14)| [fasthttprouter](https://pkg.go.dev/github.com/buaazp/fasthttprouter) (0.1) | 32 595 | 35 554 | 33 819 | 29 121 | 27 795 | 27 753 |
| 42 | python (3.8)| [clastic](https://github.com/mahmoud/clastic) (19.9) | 23 186 | 28 564 | 33 690 | 37 564 | 37 218 | 36 718 |
| 43 | crystal (0.34)| [onyx](https://onyxframework.org) (0.5) | 29 364 | 35 122 | 32 110 | 28 169 | 26 918 | 25 734 |
| 44 | python (3.8)| [molten](https://moltenframework.com) (1.0) | 20 081 | 24 727 | 31 497 | 29 515 | 30 574 | 29 577 |
| 45 | python (3.8)| [fastapi](https://fastapi.tiangolo.com) (0.54) | 23 908 | 26 528 | 30 642 | 29 253 | 28 939 | 27 423 |
| 46 | go (1.14)| [gorouter-fasthttp](https://github.com/vardius/gorouter/wiki) (4.4) | 21 253 | 26 541 | 30 497 | 34 207 | 33 873 | 33 880 |
| 47 | python (3.8)| [aiohttp](https://aiohttp.readthedocs.io) (3.6) | 17 219 | 23 573 | 28 178 | 28 981 | 27 459 | 28 191 |
| 48 | python (3.8)| [sanic](https://github.com/huge-success/sanic) (19.12) | 19 299 | 24 800 | 26 792 | 25 433 | 23 061 | 22 525 |
| 49 | python (3.8)| [blacksheep](https://github.com/RobertoPrevato/BlackSheep) (0.2) | 21 878 | 25 897 | 25 746 | 23 742 | 21 597 | 20 012 |
| 50 | python (3.8)| [responder](https://python-responder.org) (2.0) | 18 393 | 22 429 | 23 920 | 24 319 | 24 080 | 23 951 |
| 51 | ruby (2.7)| [hanami-api](https://hanamirb.org) (0.1) | 20 184 | 22 172 | 23 201 | 23 404 | 23 198 | 22 790 |
| 52 | python (3.8)| [tornado](https://tornadoweb.org) (6.0) | 16 634 | 20 929 | 21 493 | 23 639 | 24 433 | 24 567 |
| 53 | python (3.8)| [bottle](https://bottlepy.org) (0.12) | 20 486 | 20 572 | 21 091 | 27 258 | 26 657 | 25 577 |
| 54 | python (3.8)| [emmett](https://github.com/emmett-framework/emmett) (2.0.0b2) | 14 777 | 19 225 | 20 521 | 18 967 | 16 635 | 16 633 |
| 55 | ruby (2.7)| [syro](https://github.com/soveran/syro) (3.2) | 15 848 | 16 374 | 17 540 | 16 951 | 17 979 | 17 197 |
| 56 | ruby (2.7)| [rack-routing](https://github.com/georgeu2000/rack-routing) (0.0) | 15 298 | 15 958 | 16 854 | 16 390 | 16 478 | 16 194 |
| 57 | ruby (2.7)| [cuba](https://cuba.is) (3.9) | 13 889 | 14 979 | 15 513 | 15 240 | 15 546 | 15 194 |
| 58 | python (3.8)| [quart](https://pgjones.gitlab.io/quart) (0.11) | 9 978 | 13 302 | 15 033 | 16 291 | 17 001 | 16 344 |
| 59 | ruby (2.7)| [camping](https://github.com/camping/camping) (2.1) | 12 500 | 12 824 | 13 029 | 12 848 | 12 922 | 12 874 |
| 60 | ruby (2.7)| [roda](https://roda.jeremyevans.net) (3.3) | 10 827 | 11 965 | 12 002 | 12 646 | 11 881 | 12 749 |
| 61 | ruby (2.7)| [rack-app](https://rack-app.com) (7.6) | 10 188 | 10 223 | 11 223 | 10 631 | 10 821 | 10 774 |
| 62 | python (3.8)| [cherrypy](https://github.com/cherrypy/cherrypy) (18.5) | 5 131 | 7 687 | 8 685 | 9 196 | 10 454 | 10 640 |
| 63 | ruby (2.7)| [rails](https://rubyonrails.org) (6.0) | 8 094 | 7 707 | 8 064 | 8 127 | 7 944 | 8 105 |
| 64 | ruby (2.7)| [flame](https://github.com/AlexWayfer/flame) (4.18) | 7 519 | 7 844 | 8 025 | 7 888 | 8 005 | 7 800 |
| 65 | ruby (2.7)| [hanami](https://hanamirb.org) (1.3) | 7 552 | 7 579 | 7 586 | 7 434 | 7 486 | 7 371 |
| 66 | ruby (2.7)| [sinatra](https://sinatrarb.com) (2.0) | 7 311 | 7 445 | 7 369 | 7 553 | 7 547 | 7 668 |
| 67 | ruby (2.7)| [grape](https://ruby-grape.org) (1.3) | 6 429 | 6 818 | 6 830 | 6 943 | 6 783 | 6 946 |
| 68 | python (3.8)| [django](https://djangoproject.com) (3.0) | 116 | 159 | 200 | 210 | 203 | 200 |

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
