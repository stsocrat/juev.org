---
layout: post
title: "Использование профилей приложений в UFW"
date: 2016-02-14 15:03
image: 
tags: 
  - security
  - ubuntu
  - vps
---

Итак, свой [VPN-сервер мы подняли](http://www.juev.org/2016/02/12/docker-vpn/ "Свой VPN-сервер с использованием Docker"), но если привычно настроили безопасность VPS-сервера, наверное обратили внимание на то, что часть приложений просто не работает. К примеру, позвонить по FaceTime невозможно, или WhatsApp довольно долго реагирует на новые сообщения.

Решение простое: нужно открывать используемые приложениями порты. Если посмотреть описания в интернете, какие именно порты используются для ряда программ, к примеру:

> WhatsApp wrote in there own FAQ that they only use Ports between 443, 5222 + additional 5223, 5228, 4244, 5242 (tcp/udp), it's correct that initial voip support needs udp

Становиться понятым, что добавлять все порты вручную очень затруднительно и при этом менять правила в дальнейшем не представляется возможным. Что же делать? Давайте создадим профили приложения для UFW.

Для этого достаточно в директории `/etc/ufw/applications.d/` создать текстовый файл, используя синтаксис INI-файла. К примеру для FaceTime:

    [FaceTime]
    title=FaceTime
    description=Rules for FaceTime messanger
    ports=53,80,443,4080,5223,16393:16472/udp

После чего нужно инициализировать правила в UFW, для чего можно использовать команду:

    ufw reload

И затем разрешить доступ согласно созданному профилю:

    ufw allow FaceTime

Это гораздо проще и удобнее, чем постоянно перед глазами держать список портов, используемых программой. Так же легко менять эти правила, достаточно изменить файл профайла и перечитать его в UFW.

Создал репозиторий [ufw-profiles](https://github.com/Juev/ufw-profiles "ufw-profiles") в который добавил несколько профайлов для ряда программ. В дальнейшем планирую его обновлять, добавляя те или программы. Pull request только приветствуются!
