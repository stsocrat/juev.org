---
layout: post
title: Внедряем шрифты на страницы сайта
keywords: webdesign,fonts,typekit,google
date: 2010-11-14 00:00
tags:
- webdesign
- fonts
- google
---
При разработке сайтов не последнюю роль в оформлении играют выбранные шрифты. От этого выбора будет зависеть не только восприятие всего сайта в целом, но и то, что текст с различными гарнитурами читается совершенно по разному.

Самая большая сложность заключается в том, что в мире работает большое число операционных систем, с различными вариантами установленных программ. В связи с чем, на компьютерах пользователей оказывается совершенно не предсказуемый набор установленных шрифтов. А на страницах веб-сайтов присутствуют только указания на то, какие шрифты системы необходимо использовать. В связи с чем разработчик веб-сайта никогда не может полноценно предположить, как будет выглядеть его работа на компьютерах пользователей. 

Не так давно появился коммерческий проект <a href="http://typekit.com/" rel="nofollow">typekit</a>, суть которого заключается в том, что все необходимые шрифты хранятся на сервере проекта. А на страницы веб-сайта внедряется css-код, позволяющий подключить эти шрифты непосредственно с сайта проекта. Таким образом, вне зависимости от того, какая операционная система, или программное обеспечение установлены на компьютер пользователя, достигается именно то отображение, что подразумевал разработчик. 

Удручает только то, что проект платный, и тарифные планы зависят от количества подключенных сайтов, числа ежемесячно просматриваемых страниц, доступных шрифтов. И стоимость меняется от 24.99$ до 99.99$ в год. Именно поэтому я оставил мысль об использовании внедряемых шрифтов на своих сайтах.

Однако чуть позже оказалось, что Google организовал свою библиотеку свободных шрифтов, которые можно внедрять на страницы сайта. Называется проект <a href="https://fonts.google.com/" rel="nofollow">Google font directory</a>. Да, шрифтов на порядки меньше, чем используется в typekit. Но они доступны всем и без ограничений!

Использовать довольно просто. Достаточно выбрать из каталога шрифт, который хотим использовать. На странице шрифта выбираем ссылку <code>Get the code</code>. И копируем строку вида:

    <link href='http://fonts.googleapis.com/css?family=Droid+Sans&subset=latin' rel='stylesheet' type='text/css'>

в блок <code>head</code> страниц, на которых собираемся внедрять шрифты. Осталось только прописать в стилях страницы используемый шрифт, например:

    h1 { font-family: 'Droid Sans', arial, serif; }

Приведенные примеры используют шрифт <code>Droid Sans</code>. После данной операции шрифт будет подгружаться с сервера Google и страницы на всех системах будут отображаться одинаково.
