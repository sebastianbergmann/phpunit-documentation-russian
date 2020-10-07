.. _installation:

=================
Установка PHPUnit
=================

.. _installation.requirements:

Требования
##########

PHPUnit |version| требует PHP 7.2; настоятельно рекомендуется использовать последнюю версию PHP.

PHPUnit требует расширений `dom <http://php.net/manual/ru/dom.setup.php>`_ и `json <http://php.net/manual/ru/json.installation.php>`_,
которые обычно включены по умолчанию.

PHPUnit также требует расширений
`pcre <http://php.net/manual/ru/pcre.installation.php>`_,
`reflection <http://php.net/manual/ru/reflection.installation.php>`_
и `spl <http://php.net/manual/ru/spl.installation.php>`_.
Эти стандартные расширения включены по умолчанию и не могут быть
отключены без внесения изменений в систему сборки PHP и/или в исходный код C.

Для функциональности отчёта по покрытию кода тестами требуются расширения
`Xdebug <http://xdebug.org/>`_ (2.7.0 или новее) и
`tokenizer <http://php.net/manual/ru/tokenizer.installation.php>`_.
Генерация XML-отчётов требует расширения
`xmlwriter <http://php.net/manual/ru/xmlwriter.installation.php>`_.

.. _installation.phar:

PHP Archive (PHAR)
##################

Самый простой способ получить PHPUnit — загрузить `PHP Archive (PHAR) <http://php.net/phar>`_,
в котором есть все необходимые (а также некоторые необязательные) зависимости PHPUnit,
собранные в одном-единственном файле.

Расширение `phar <http://php.net/manual/ru/phar.installation.php>`_ обязательно
для использования PHP Archives (PHAR).

Если расширение `Suhosin <http://suhosin.org/>`_ включено,
вам необходимо разрешить выполнение PHAR в вашем
``php.ini``:

.. parsed-literal::

    suhosin.executor.include.whitelist = phar

PHAR с PHPUnit можно использовать сразу после загрузки:

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ php phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

Как правило, далее необходимо сделать исполняемым PHAR-файл:

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ chmod +x phpunit-|version|.phar
    $ ./phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

.. _installation.phar.verification:

Проверка релизов PHPUnit PHAR
=============================

Все официальные релизы кода, распространяемые проектом PHPUnit, подписываются
релиз-менеджером. Подписи PGP и хеши SHA256
доступны для проверки на `phar.phpunit.de <https://phar.phpunit.de/>`_.

В следующем примере показано, как работает проверка релиза. Мы начинаем
с загрузки :file:`phpunit.phar`, а также его отделённой подписи PGP
:file:`phpunit.phar.asc`:

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ wget https://phar.phpunit.de/phpunit-|version|.phar.asc

Мы хотим проверить PHP Archive (:file:`phpunit-x.y.phar`) PHPUnit
с его отделённой подписью (:file:`phpunit-x.y.phar.asc`):

.. parsed-literal::

    $ gpg phpunit-|version|.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Can't check signature: public key not found

У нас нет открытого ключа релиз-менеджера (``6372C20A``)
в нашей локальной системе. Для продолжения проверки нам нужно
получить открытый ключ релиз-менеджера с сервера ключей. Один из таких
серверов — это :file:`pgp.uni-mainz.de`. Серверы открытых ключей
связаны между собой, поэтому вы можете подключиться к любому из них.

.. parsed-literal::

    $ gpg --keyserver pgp.uni-mainz.de --recv-keys 0x4AA394086372C20A
    gpg: requesting key 6372C20A from hkp server pgp.uni-mainz.de
    gpg: key 6372C20A: public key "Sebastian Bergmann <sb@sebastian-bergmann.de>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)

Теперь мы получили открытый ключ для сущности, известной как "Sebastian
Bergmann <sb@sebastian-bergmann.de>". Однако, у нас нет способа
проверить, что этот ключ был создан человеком под именем Себастьян Бергман (Sebastian
Bergmann). Но давайте снова попробуем проверить подпись релиза.

.. parsed-literal::

    $ gpg phpunit-|version|.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Good signature from "Sebastian Bergmann <sb@sebastian-bergmann.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian@php.net>"
    gpg:                 aka "Sebastian Bergmann <sebastian@thephp.cc>"
    gpg:                 aka "Sebastian Bergmann <sebastian@phpunit.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian.bergmann@thephp.cc>"
    gpg:                 aka "[jpeg image of size 40635]"
    gpg: WARNING: This key is not certified with a trusted signature!
    gpg:          There is no indication that the signature belongs to the owner.
    Primary key fingerprint: D840 6D0D 8294 7747 2937  7831 4AA3 9408 6372 C20A

В данный момент подпись хорошая, но мы не доверяем этому ключу. Хорошая
подпись означает, что файл не был изменён. Однако ввиду характера
криптографии открытого ключа вам необходимо дополнительно
проверить, что ключ ``6372C20A`` был создан настоящим
Себастьяном Бергманом.

Любой злоумышленник может создать открытый ключ и загрузить его на серверы открытых серверов.
Затем они могут создать вредоносный релиз, подписанный этим поддельным ключом.
После чего, если вы попытаетесь проверить подпись этого испорченного релиза,
проверка будет успешной, потому что ключ не является «реальным» ключом. Поэтому вам
нужно проверить подлинность этого ключа. Однако проверка подлинности открытого ключа
выходит за рамки данной документации.

Проверка подлинности и целостности PHAR с PHPUnit вручную через GPG утомительна. Вот зачем нужен PHIVE (PHAR Installation and Verification Environment), среда установки и проверки PHAR. Вы можете узнать про PHIVE на `сайте <https://phar.io/>`_.

.. _installation.composer:

Composer
########

Просто добавьте (для разработки) зависимость
``phpunit/phpunit`` в файл
``composer.json`` вашего проекта, если вы используете `Composer <https://getcomposer.org/>`_ для управления
зависимостями в вашем проекте:

.. parsed-literal::

    composer require --dev phpunit/phpunit ^\ |version|

.. _installation.global:

Глобальная установка
####################

Обратите внимание, что не рекомендуется устанавливать PHPUnit глобально, например, в ``/usr/bin/phpunit`` или
``/usr/local/bin/phpunit``.

Вместо этого PHPUnit должен использоваться в виде локальной зависимости проекта.

Поэтому либо поместите PHAR определённой версии PHPUnit, которая вам нужна, в директорию ``tools`` вашего проекта (который должен управляться с помощью PHIVE), либо укажите конкретную версию PHPUnit в файле ``composer.json`` вашего проекта, если вы используете Composer.
