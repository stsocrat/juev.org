---
layout: post
title: WordPress AntiSpam
keywords: wordpress,spam,antispam,akismet,wp-spamfree,nospamnx,amcaptcha
date: 2010-01-05 00:00
tags:
- wordpress
- spam
---
WordPress -- самый популярный блогодвижок в мире. Почему? Все просто, его очень просто устанавливать, с ним очень просто работать, у него невысокие требования по хостингу, у него широкие возможности, у него очень много готовых расширений и шаблонов оформления. Естественно, что именно эта платформа подвергается атаке спамеров в большей степени.

Самый эффективный способ борьбы со спамом -- это закрытие возможности анонимных коментов. Предоставить возможность зарегистрироваться пользователю, если он хочет оставить комментарий и пусть работает... Естественно, что данный способ уменьшает количество самих коментов, поэтому идем дальше.

И в связи с этим было выпущено большое число плагинов wordpress, которые призваны решить эту проблему. Но какие выбрать? Что именно использовать? Попробуем вместе ответить на данный вопрос.

Прежде всего необходимо понимать, что спам бывает двух типов:
<ol>
	<li>спам-боты, то есть автоматический спам;</li>
	<li>спам-комментарии от посетителей.</li>
</ol>
И если для борьбы со вторым достаточно только установить <a
href="http://wordpress.org/extend/plugins/akismet/" rel="nofollow">Akismet</a>, плагин, который идет в стандартной поставке wordpress, то бороться с первым более проблематично. Самое оптимальное -- это использовать капчу. Но с капчей есть несколько сложностей.

Во-первых, она затрудняет комментирование, и тем самым уменьшает количество комментаторов. А во-вторых, не все капчи эффективны. Уже есть спам-боты, которые легко обходят некоторые виды капч.

За несколько месяцев работы с Wordpress у меня появился некоторый опыт в работе с антиспам-плагинами, чем с вами и делюсь.

Я уже не помню точно, с чего именно я начинал, но знаю наверняка, что самая эффективная связка -- это <a href="http://wordpress.org/extend/plugins/akismet/" rel="nofollow">Akismet</a> + <a href="http://wordpress.org/extend/plugins/wp-spamfree/" rel="nofollow">WP-SpamFree</a>, первый плагин, как я уже указывал, позволяет отсеивать спам от людей (на сегодняшний день на моем блоге он отсеял уже более 6000 спам-комментариев), а второй позволяет отсеивать ботов. При этом пользователю ничего не нужно вводить, определение спам-бота производиться с помощью javascripts и cookie. За время работы на моем блоге WP-SpamFree сумел отразить более 2000 спам-комментариев. Но! Есть одно но, которое заставило меня искать другое решение. А именно размер самого используемого скрипта. Использование данного плагина замедляло загрузку сайта примерно на полсекунды. Мелочь? Да, но когда борешься за каждую миллисекунду -- это уже много...

Попробовал в работе <a href="http://wordpress.org/extend/plugins/nospamnx/" rel="nofollow">NoSpamNX</a>, о чем указывал в блоге. Но знакомство с ним продолжалось не долго. Просто потому, что почему то в моем случае он установился не корректно и комментарии не пропускал совершенно. Спасибо, что обратили мое внимание на данный факт!

А на данный момент я использую плагин <a href="http://wordpress.org/extend/plugins/amcaptcha/" rel="nofollow">amcaptcha</a>, принцип действия которой заключается в добавлении еще одного поля в форму комментирования. Точнее добавляется только чеклист, который нужно установить, для того, чтобы комент признали за неспам. Просто? Да! И что самое удивительное -- работает! При этом никак не влияет на скорость загрузки блога.

Подведем итог? Akismet -- обязателен к использованию! В качестве дополнения использовать или WP-SpamFree или AmCaptcha, в зависимости от ваших требований. Первый ведет статистику отсеивания спама и доказал свою эффективность, но несколько замедляет загрузку страниц сайта, второй легкий и просто эффективный.

В статье я описал только те решения, что опробовал сам на практике. На деле существует просто громадное число плагинов для Wordpress, которые позволяют бороться со спамом. Но я надеюсь, что мой опыт хотя бы на немного облегчит вашу жизнь!
