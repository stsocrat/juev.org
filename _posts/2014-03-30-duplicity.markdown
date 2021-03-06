---
layout: post
title: Резервная копия VPS на серверах Amazon
description: 
keywords: amazon, s3, duply, duplicity, crypt, backup, security
gplus: 
published: true
date: 2014-03-30 19:23
tags:
- amazon
- security
- backup
---
Программисты шутят: люди делятся на тех, кто НЕ делает бэкап и тех, кто его УЖЕ делает.

Резервную копию своего сервера можно хранить и на серверах Amazon. Про низкую стоимость сервисов S3  все уже слышали. А тут еще с [1 апреля 2014 года][6] стоимость падает на 65%. Грех не воспользоваться.

## Установка и настройка программного обеспечения

Во-первых, установим все необходимое программное обеспечение:

    $ sudo apt-get install duplicity duply python-boto

Теперь создаем профиль для работы с серверами Амазона:

    $ duply main create

В данном случае `main` это имя профиля, которые мы выбираем самостоятельно, и оно может быть любым. В профиле размещается файл конфигурации `$HOME/.duply/main/conf` с которым мы в дальнейшем будем работать. Программа Duplicity, с помощью которой будет осуществляться загрузка данных, поддерживает шифрование, причем как асимметричное, так и с использованием пароля. Использовать пароль это самое простое, именно этот вариант и будем использовать.

Для генерации случайного пароля можно использовать команду:

    $ openssl rand -base64 20

И теперь в файле конфигурации комментируем строку `GPG_KEY` и задаем пароль в строке `GPG_PW`:

    #GPG_KEY='_KEY_ID_'
    GPG_PW='<the password you just got from openssl>'

Если же вы планируете вообще отключить шифрование данных, то в строке `GPG_KEY` необходимо задать значение `disabled`.

Опускаемся ниже до строк TARGET:

    TARGET='s3://s3-<region endpoint name>.amazonaws.com/<bucket name>/<folder name>'
    TARGET_USER='<your AWS access key ID>'
    TARGET_PASS='<your AWS secret key>'

В `TARGET` задается адрес корзины и директории, в которой будут размещаться резервные копии. И чтобы не думать о правильности заполнения региона, проще всего его задать в виде:

    TARGET='s3+http://<bucket name>/<folder name>

Теперь самое главное, необходимо задать директории, которые планируется включить в резервную копию. Здесь есть небольшая тонкость, задать можно только одну директорию, поэтому всегда выбирается самая верхняя, относительно всех включаемых, директория, и часто это оказывается именно корень файловой системы:

    SOURCE='/'

Продолжим задавать требуемые директории чуть позже, так как для того используется другой файл, а пока закончим основное конфигурирование. По умолчанию время хранения полного бекапа составляет один месяц, то есть через месяц полный бекап с сервера удаляется и резервная копия создается заново. Если это не устраивает, настройку можно изменить с помощью параметра:

    MAX_AGE=6M

Так же стоит обратить внимание на параметр, задающий размер архива, по умолчанию он равен 25 мегабайт, увеличим его вдвое:

    VOLSIZE=50
    DUPL_PARAMS="$DUPL_PARAMS --volsize $VOLSIZE "

Теперь можно сохранить и закрыть файл конфигурации.

Вот теперь продолжаем задавать список директорий, которые мы хотим включить в резервную копию. Для этого создаем файл `$HOME/.duply/main/exclude` со следующим содержанием:

    + /etc
    + /var/www
    - **

Формат файла простой, плюс/минус в начале строки задают включение/исключение директорий или файлов. Причем сначала описываются все включения и только затем исключения. Поддерживаются так же регэкспы, как в приведенном выше примере. 

В качестве основной директории мы задали корень файловой системы, и теперь в файле exclude определили, что из всего набора директорий нам необходимо включать в резервную копию только `/etc` и `/var/www`, а все остальные директории и файлы будут исключаться из резервной копии.

После задания всех настроек необходимо будет перейти на сайт [aws.amazon.com][1] и в консоли управления S3 создать bucket (корзину) для хранения резервных копий.

## Инициализация резервной копии

После проведения настроек потребуется провести пробное создание резервной копии. При первом запуске проводится полный бекап, и часто он занимает продолжительное время (зависит от объемов включаемых данных). Чтобы не возникало проблем, воспользуемся программой tmux:

    $ sudo apt-get install tmux
    $ tmux

Теперь запускаем создание резервной копии:

    $ sudo duply main backup

После чего используем сочетание клавиш `Ctrl+b d` для того, чтобы отсоединиться от текущей сессии tmux. Теперь можно спокойно закрывать ssh-соединение, программа продолжит свою работу в фоне.

Возвращаемся позже, соединяемся с сервером и подключаемся к сессии tmux:

    $ tmux attach

Если увидите в консоли подобное сообщение, значит создание резервной копии прошло успешно:

    --------------[ Backup Statistics ]--------------
    ...
    Errors 0
    -------------------------------------------------
    
    --- Finished state OK at 16:48:16.192 - Runtime 01:17:08.540 ---
    
    --- Start running command POST at 16:48:16.213 ---
    Skipping n/a script '/home/admin/.duply/main/post'.
    --- Finished state OK at 16:48:16.244 - Runtime 00:00:00.031 ---

## Автоматический запуск с использованием cron

Для того чтобы проводить запуск duply регулярно, достаточно использовать cron:

    $ sudo crontab -e

Добавляем в конец файла следующую строчку:

    0 2 * * * env HOME=/home/admin duply main backup

В данном случае запуск будет проводиться ежедневно в 2 часа утра. Более подробно по формату задания времени можно узнать в справке cron.

## Восстановление из резервной копии

### Простое восстановление

Для восстановления последнего состояния файлов используется команда `restore`. При этом указывается директория, в которой будут размещаться восстанавливаемые файлы:

    $ sudo duply main restore /restored_files

И после восстановления уже перемещаем файлы/директории в требуемое место. Не пытайтесь использовать команду `sudo duply main restore /` так как системные файлы (такие как bash, libc и другие) наверняка будут заняты. И восстановление потребует загрузку сервера в режиме восстановления, после чего файлы можно будет перенести на свои законные места.

### Восстановление определенных файлов или директорий

Для восстановления определенных файлов или директорий используется команда `fetch`. К примеру, для восстановления файла `/etc/password` из бекапа и размещения его в директории `/home/admin/password ` используется команда:

    $ sudo duply main fetch etc/password /home/admin/password

Обратите внимание на то, что при указании файла, который восстанавливаем, первая косая черта пропущена специально, это не ошибка. Так же команда `fetch` работает и с директориями:

    $ sudo duply main fetch etc /home/admin/etc

### Восстановление файлов на определенную дату

По умолчанию производиться восстановление последнего состояния файлов в бекапе. Но можно провести восстановление и предыдущих состояний. Для этого достаточно узнать, когда именно проводился бекап в предыдущие разы, для этого используется команда `status`:

    $ duply main status
    ...
    Number of contained backup sets: 2
    Total number of contained volumes: 2 
    Type of backup set:                            Time:      Num volumes:
                   Full         Sat Nov  8 07:38:30 2013                 1
            Incremental         Sat Nov  9 07:43:17 2013                 1
    ...

В данном примере проведем восстановление из резервной копии за 8 ноября:

    $ sudo duply test restore /restored_files '2013-11-08T07:38:30'

Обратите внимание на то, что дата задается в формате w3. Про формат времени более подробно можно почитать на странице [the Time Formats section in the Duplicity man page][2].

## Осторожность в хранении ключей/пароля

После того, как будет проведена настройка резервных копий, не забудьте сохранить пароль или ключи, что были заданы в файле конфигурации. Причем сохранять их нужно НЕ на сервере. И если с сервером что-нибудь произойдет, а под рукой не окажется пароля или ключа, с помощью которого проводилось шифрование данных, то восстановить файлы будет уже не возможно.

Поэтому храните пароли и ключи в программе [1Password][3] и при этом дублируйте данные на телефон, планшет или другие сервера. Или же используйте альтернативные программы/сервисы, типа [LastPass][4] или [KeePass][5].

[1]: https://aws.amazon.com "Amazon"
[2]: http://duplicity.nongnu.org/duplicity.1.html#toc9
[3]: https://agilebits.com/onepassword
[4]: https://lastpass.com/
[5]: http://keepass.info/
[6]: http://aws.typepad.com/aws/2014/03/aws-price-reduction-42-ec2-s3-rds-elasticache-and-elastic-mapreduce.html
