.. _installation:

=================
Установка PHPUnit
=================

.. _installation.requirements:

Требования
##########

PHPUnit |version| требует PHP 7.1; настоятельно рекомендуется использовать последнюю версию PHP.

PHPUnit требует расширений `dom <http://php.net/manual/ru/dom.setup.php>`_ и `json <http://php.net/manual/ru/json.installation.php>`_,
которые обычно включены по умолчанию.

PHPUnit также требует расширений
`pcre <http://php.net/manual/ru/pcre.installation.php>`_,
`reflection <http://php.net/manual/ru/reflection.installation.php>`_
и `spl <http://php.net/manual/ru/spl.installation.php>`_.
Эти стандартные расширения включены по умолчанию и не могут быть
отключены без внесения изменений в систему сборки PHP и/или в исходный код C.

Для функциональности отчёта по покрытию кода тестами требуются расширения
`Xdebug <http://xdebug.org/>`_ (2.5.0 или новее) и
`tokenizer <http://php.net/manual/ru/tokenizer.installation.php>`_.
Генерация XML-отчётов требует расширения
`xmlwriter <http://php.net/manual/ru/xmlwriter.installation.php>`_.

.. _installation.phar:

PHP Archive (PHAR)
##################

Самый простой способ получить PHPUnit - загрузить `PHP Archive (PHAR) <http://php.net/phar>`_, в котором есть
все необходимые (а также некоторые необязательные) зависимости PHPUnit,
собранные в одном-единственном файле.

Расширение `phar <http://php.net/manual/ru/phar.installation.php>`_ обязательно
для использования PHP Archives (PHAR).

Если расширение `Suhosin <http://suhosin.org/>`_ включено,
вам необходимо разрешить выполнение PHAR в вашем
``php.ini``:

.. code-block:: bash

    suhosin.executor.include.whitelist = phar

Для глобальной установки PHAR:

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-|version|.phar
    $  chmod +x phpunit-|version|.phar
    $  sudo mv phpunit-|version|.phar /usr/local/bin/phpunit
    $  phpunit --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

Вы также можете использовать загруженный PHAR-файл напрямую:

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-|version|.phar
    $  php phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

.. _installation.phar.windows:

Windows
=======

Глобальная установка PHAR включает ту же самую процедуру, что и ручная
`установка Composer на Windows <https://getcomposer.org/doc/00-intro.md#installation-windows>`_:

#.

   Создать папку для двоичных файлов PHP; например, :file:`C:\\bin`

#.

   Добавить ;C:\bin к вашей переменной окружения ``PATH``
   (`справочная информация по этой теме <http://stackoverflow.com/questions/6318156/adding-python-path-on-windows-7>`_)

#.

   Загрузить `<https://phar.phpunit.de/phpunit-|version|.phar>`_ и
   сохранить файл по пути :file:`C:\\bin\\phpunit.phar`

#.

   Открыть командую строку (например,
   нажать :kbd:`Windows`:kbd:`R`
   » ввести cmd
   » :kbd:`ENTER`)

#.

   Создать пакетный скрипт (в результате получится
   :file:`C:\\bin\\phpunit.cmd`):

   .. code-block:: bash

       C:\Users\username>  cd C:\bin
       C:\bin>  echo @php "%~dp0phpunit.phar" %* > phpunit.cmd
       C:\bin>  exit

#.

   Открыть новую командную строку и убедиться, что вы можете выполнить PHPUnit
   из любого пути:

   .. code-block:: bash

       C:\Users\username>  phpunit --version
       PHPUnit x.y.z by Sebastian Bergmann and contributors.

Для командных оболочек Cygwin и/или MingW32 (например, TortoiseGit) вы
можете пропустить шаг 5 выше, просто сохранив файл как
:file:`phpunit` (без расширения :file:`.phar`)
и сделав его исполняемым через команду
chmod 775 phpunit.

.. _installation.phar.verification:

Проверка релизов PHPUnit PHAR
=============================

Все официальные релизы кода, распространяемые проектом PHPUnit, подписываются
релиз-менеджером. Подписи PGP и хеши SHA1
доступны для проверки на `phar.phpunit.de <https://phar.phpunit.de/>`_.

В следующем примере показано, как работает проверка релиза. Мы начинаем
с загрузки :file:`phpunit.phar`, а также его отделённой подписи PGP
:file:`phpunit.phar.asc`:

.. code-block:: bash

    wget https://phar.phpunit.de/phpunit.phar
    wget https://phar.phpunit.de/phpunit.phar.asc

Мы хотим проверить PHP Archive (:file:`phpunit.phar`) PHPUnit
с его отделённой подписью (:file:`phpunit.phar.asc`):

.. code-block:: bash

    gpg phpunit.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Can't check signature: public key not found

У нас нет открытого ключа релиз-менеджера (``6372C20A``)
в нашей локальной системе. Для продолжения проверки нам нужно
получить открытый ключ релиз-менеджера с сервера ключей. Один из таких
серверов - это :file:`pgp.uni-mainz.de`. Серверы открытых ключей
связаны между собой, поэтому вы можете подключиться к любому из них.

.. code-block:: bash

    gpg --keyserver pgp.uni-mainz.de --recv-keys 0x4AA394086372C20A
    gpg: requesting key 6372C20A from hkp server pgp.uni-mainz.de
    gpg: key 6372C20A: public key "Sebastian Bergmann <sb@sebastian-bergmann.de>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)

Теперь мы получили открытый для сущности, известной как "Sebastian
Bergmann <sb@sebastian-bergmann.de>". Однако, у нас нет способа
проверить, что этот ключ был создан человеком под именем Себастьян Бергман (Sebastian
Bergmann). Но давайте снова попробуем проверить подпись релиза.

.. code-block:: bash

    gpg phpunit.phar.asc
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
Себастьяном Бергманом (Sebastian Bergmann).

Любой злоумышленник может создать открытый ключ и загрузить его на серверы открытых серверов.
Затем они могут создать вредоносный релиз, подписанный этим поддельным ключом.
key. После чего, если вы попытаетесь проверить подпись этого испорченного релиза,
проверка будет успешной, потому что ключ не является "реальным" ключом. Поэтому вам
нужно проверить подлинность этого ключа. Однако проверка подлинности открытого ключа
выходит за рамки данной документации.

Возможно, было бы целесообразно создать скрипт командной оболочки для управления установкой PHPUnit,
который проверяет подпись GnuPG перед запуском набора тестов. Например:

.. code-block:: bash

    #!/usr/bin/env bash
    clean=1 # Delete phpunit.phar after the tests are complete?
    aftercmd="php phpunit.phar --bootstrap bootstrap.php src/tests"
    gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
    if [ $? -ne 0 ]; then
        echo -e "\033[33mDownloading PGP Public Key...\033[0m"
        gpg --recv-keys D8406D0D82947747293778314AA394086372C20A
        # Sebastian Bergmann <sb@sebastian-bergmann.de>
        gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
        if [ $? -ne 0 ]; then
            echo -e "\033[31mCould not download PGP public key for verification\033[0m"
            exit
        fi
    fi

    if [ "$clean" -eq 1 ]; then
        # Let's clean them up, if they exist
        if [ -f phpunit.phar ]; then
            rm -f phpunit.phar
        fi
        if [ -f phpunit.phar.asc ]; then
            rm -f phpunit.phar.asc
        fi
    fi

    # Let's grab the latest release and its signature
    if [ ! -f phpunit.phar ]; then
        wget https://phar.phpunit.de/phpunit.phar
    fi
    if [ ! -f phpunit.phar.asc ]; then
        wget https://phar.phpunit.de/phpunit.phar.asc
    fi

    # Verify before running
    gpg --verify phpunit.phar.asc phpunit.phar
    if [ $? -eq 0 ]; then
        echo
        echo -e "\033[33mBegin Unit Testing\033[0m"
        # Run the testing suite
        `$after_cmd`
        # Cleanup
        if [ "$clean" -eq 1 ]; then
            echo -e "\033[32mCleaning Up!\033[0m"
            rm -f phpunit.phar
            rm -f phpunit.phar.asc
        fi
    else
        echo
        chmod -x phpunit.phar
        mv phpunit.phar /tmp/bad-phpunit.phar
        mv phpunit.phar.asc /tmp/bad-phpunit.phar.asc
        echo -e "\033[31mSignature did not match! PHPUnit has been moved to /tmp/bad-phpunit.phar\033[0m"
        exit 1
    fi

.. _installation.composer:

Composer
########

Просто добавьте (для разработки) зависимость
``phpunit/phpunit`` в файл
``composer.json`` вашего проекта, если вы используете `Composer <https://getcomposer.org/>`_ для управления
зависимостями в вашей проекте:

.. code-block:: bash

    composer require --dev phpunit/phpunit ^|version|

.. _installation.optional-packages:

Необязательные пакеты
#####################

Доступны следующие необязательные (дополнительные) пакеты:

``PHP_Invoker``

    Класс-утилита для вызова функций обратного вызова с тайм-аутом. Этот пакет
    необходим для обеспечения выполнения тайм-аутов тестирования в строгом режиме.

    Этот пакет включён в дистрибутив PHAR PHPUnit. Его
    можно установить через Composer, используя следующую команду:

    .. code-block:: bash

        composer require --dev phpunit/php-invoker

``DbUnit``

    Порт DbUnit для PHP/PHPUnit для поддержки тестирования взаимодействия с базами данных.

    Этот пакет не включён в дистрибутив PHAR PHPUnit. Его
    можно установить через Composer, используя следующую команду:

    .. code-block:: bash

        composer require --dev phpunit/dbunit
