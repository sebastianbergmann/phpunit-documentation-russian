.. _organizing-tests:

==================
Организация тестов
==================

Одна из целей PHPUnit заключается в том, что
тесты должны быть составными: мы хотим запускать любое количество или комбинацию
тестов вместе, например, все тесты для всего проекта, либо тесты
всех классов компонента, который является частью проекта, либо просто
тесты для одного класса.

PHPUnit поддерживает различные способы организации тестов и составления их
в набор тестов. В этой главе показаны наиболее часто используемые подходы.

.. _organizing-tests.filesystem:

Составление набора тестов с помощью файловой системы
####################################################

Возможно, самый простой способ составить набор тестов — это держать все исходные файлы тестов в тестовом каталоге.
PHPUnit может автоматически обнаруживать и запускать тесты путём рекурсивного обхода тестового каталога.

Давайте посмотрим на набор тестов библиотеки
`sebastianbergmann/money <http://github.com/sebastianbergmann/money/>`_.
Просматривая структуру каталогов этого проекта, мы видим, что
классы тестов в каталоге :file:`tests` отражают
структуру пакета и классов тестируемой системы в каталоге
:file:`src`:

.. code-block:: bash

    src                                 tests
    `-- Currency.php                    `-- CurrencyTest.php
    `-- IntlFormatter.php               `-- IntlFormatterTest.php
    `-- Money.php                       `-- MoneyTest.php
    `-- autoload.php

Для запуска всех тестов библиотеки нам просто нужно указать исполнителю тестов командной строки PHPUnit
каталог с тестами:

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php tests
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .................................

    Time: 636 ms, Memory: 3.50Mb

    OK (33 tests, 52 assertions)

.. admonition:: Примечание

   Если вы укажите исполнителю тестов командной строки PHPUnit каталог, он
   будет искать файлы с маской :file:`*Test.php`

Для запуска только тестов, объявленных в классе ``CurrencyTest``, находящегося
в файле :file:`tests/CurrencyTest.php`, мы можем использовать
следующую команду:

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php tests/CurrencyTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ........

    Time: 280 ms, Memory: 2.75Mb

    OK (8 tests, 8 assertions)

Для более точного контроля, какие тесты запускать, мы можем использовать опцию ``--filter``:

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php --filter testObjectCanBeConstructedForValidConstructorArgument tests
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ..

    Time: 167 ms, Memory: 3.00Mb

    OK (2 test, 2 assertions)

.. admonition:: Примечание

   Недостатком этого подхода является то, что мы не можем контролировать порядок выполнения тестов.
   Это может привести к проблемам с зависимостями теста
   см. :ref:`writing-tests-for-phpunit.test-dependencies`.
   В следующем разделе вы увидите, как можно явно задать порядок выполнения тестов,
   используя конфигурационный XML-файл.

.. _organizing-tests.xml-configuration:

Составление набора тестов с помощью конфигурации XML
####################################################

XML-файл конфигурации PHPUnit (:ref:`appendixes.configuration`)
также может использоваться для составления набора тестов.
:numref:`organizing-tests.xml-configuration.examples.phpunit.xml`
показывает файл :file:`phpunit.xml` с минимальной настройкой, который добавит все
классы ``*Test``, находящиеся в файлах
:file:`*Test.php`, после рекурсивного обхода каталога :file:`tests`.

.. code-block:: php
    :caption: Составление набора тестов, используя конфигурацию XML
    :name: organizing-tests.xml-configuration.examples.phpunit.xml

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <directory>tests</directory>
        </testsuite>
      </testsuites>
    </phpunit>

Если :file:`phpunit.xml` или
:file:`phpunit.xml.dist` (в этом порядке) существует в
текущем рабочем каталоге, а опция ``--configuration``
*не* используется, то конфигурация будет автоматически
считана из этого файла.

Порядок выполнения тестов можно сделать явным:

.. code-block:: php
    :caption: Составление набора тестов, используя конфигурацию XML
    :name: organizing-tests.xml-configuration.examples.phpunit.xml2

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <file>tests/IntlFormatterTest.php</file>
          <file>tests/MoneyTest.php</file>
          <file>tests/CurrencyTest.php</file>
        </testsuite>
      </testsuites>
    </phpunit>


