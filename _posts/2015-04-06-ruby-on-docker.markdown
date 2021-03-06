---
layout: post
title: Ruby on Docker
date: 2015-04-06 09:14
image: https://static.juev.org/2015/04/docker.png
tags:
- ruby
- docker
- jekyll
---

Сложности в использовании Ruby в Windows возникают при попытке установить расширение, требующее компиляцию. Используются многочисленные хаки для того, чтобы обойти эти ограничения, либо, что чаще всего происходит, рекомендуют не использовать Windows вообще.

Для решения данной проблемы и упрощения установки Ruby, решил использовать [Docker](http://www.docker.com "Docker"). Основная задача, что передо мной стояла -- использовать движок Jekyll без ограничений на любой платформе.

Сначала обратил свое внимание на готовые образы. В официальном образе от разработчиков [Jekyll](https://github.com/jekyll/docker "jekyll/jekyll") столкнулся с несколькими проблемами:

1. Для запуска Jekyll используется алиас, в котором строго задается расположение исходного кода сайта. В итоге параметры, заданные в конфигурации сайта игнорируются. И компиляция сайта завершается с ошибкой.
2. Образ сконфигурирован таким образом, что все сторонние расширения, если они используются, устанавливаются в сам образ, таким образом создается отдельный контейнер. Если есть несколько сайтов с различной конфигураций расширений, потребуется или все их устанавливать в один образ, или же создавать несколько отдельных контейнеров для каждого сайта.
3. Установка сторонних расширений, требующих компиляции невозможна, ввиду отсутствия необходимых библиотек.
4. В образе отсутствует библиотека GSL, которая позволяет ускорить обработку связей между статьями в десятки раз.

В образе с [Ruby](https://github.com/docker-library/ruby "Ruby") используется совершенно иной подход. Исходный код копируется в создаваемый образ и запускается из него. Это логично и правильно, но не приемлемо для моего случая, где требуется запускать Jekyll с различной конфигурацией, а создавать отдельные образы для каждого сайта, на мой взгляд, избыточно.

В конце концов, взяв за основу Dockerfile от официальной сборки Jekyll, я его переделал и создал свой собственный образ [juev/ruby](https://github.com/Juev/dockerfiles/tree/master/ruby "juev/ruby"). Данный образ ориентирован на использование языка программирования Ruby в окружении Docker.

Для его использования достаточно [установить Docker](https://docs.docker.com/installation/ "Docker Installation Page") на компьютер. И затем скачать образ:

    $ docker pull juev/ruby

Или же создать образ самому:

    $ git clone [https://github.com/Juev/dockerfiles.git](https://github.com/Juev/dockerfiles.git)
    $ cd dockerfiles/ruby
    $ docker build --no-cache --force-rm -t juev/ruby -f Dockerfile .

В поставку включены ruby v.1.9.3, nodejs 0.10.38, bundler, библиотека GSL и сторонние библиотеки для сборки расширений. Образ сконфигурирован таким образом, что bundler производит установку расширений (gems) в локальную директорию vendor/bundle.

Таким образом, для установки программных модулей и расширений, достаточно в корневой директории приложения создать файл Gemfile, к примеру для моего сайта:

    source "https://rubygems.org"

    gem 'rake'
    gem 'jekyll'
    gem 'jekyll-tagging'
    gem 'compass'
    gem 'rouge'

И затем дать команду для установки указанных зависимостей:

    $ docker run --rm -v $(pwd):/srv/work  juev/ruby bundle install

В корневой директории будет создана директория `vendor/bundle` в которой будут размещены все зависимости. Директория `vendor/bundle/bin` уже прописана в переменной PATH, поэтому можно запускать новые команды без префикса `bundle exec`.

Для того, чтобы запустить irb используем команду:

    $ docker run -it --rm -v $(pwd):/srv/work juev/ruby irb

А для сборки Jekyll сайта, находясь в рабочей директории:

    $ docker run --rm -v $(pwd):/srv/work juev/ruby jekyll build

Или, если определены цели сборки в Rakefile:

    $ docker run --rm -v $(pwd):/srv/work juev/ruby rake build

Для того, чтобы использовать Jekyll в режиме слежения и предпросмотра, достаточно указать параметр для проброса портов:

    $ docker run --rm -v $(pwd):/srv/work -p 127.0.0.1:4000:4000 juev/ruby jekyll serve

Есть сомнение по поводу того, как я назвал образ. Все таки при создании я ориентировался только на запуск Jekyll (по крайней мере пока). Но самое главное, что теперь есть возможность использовать Ruby в среде Windows, а так же не нужно больше мучиться с установкой библиотеки GSL, что используется для ускорения построения связей между статьями в среде Jekyll.

Для использования Ruby теперь достаточно установить Docker и скачать образ [juev/ruby](https://registry.hub.docker.com/u/juev/ruby/ "juev/ruby on HUB Docker"). Что может быть проще?
