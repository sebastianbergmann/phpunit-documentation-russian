.. _incomplete-and-skipped-tests:

============================
Неполные и пропущенные тесты
============================

.. _incomplete-and-skipped-tests.incomplete-tests:

Неполные тесты
##############

Когда вы работаете над новым тестовым классом, вы можете начать
с написания пустых тестовых методов, например:

.. code-block:: php

    public function testSomething()
    {
    }

для отслеживания тех тестов, которые нужно написать. Проблема
с пустыми тестовыми методами состоит в том, что фреймворком PHPUnit
они интерпретируется как успешно пройденные. Это ошибочное толкование приводит к тому,
что отчёты о покрытии становятся бесполезными — вы не сможете увидеть,
действительно ли тест прошёл, либо он просто ещё не реализован.
Вызов ``$this->fail()`` в нереализованном тестовом методе
также не поможет, поскольку тогда тест будет интерпретироваться как
не пройденный. Это было бы так же неверно, как и считать нереализованный тест как пройденный.

Если мы думаем об успешном тестировании как о зелёном свете, а о непройденном тесте как
о красном цвете, то нам нужен дополнительный жёлтый цвет для обозначения теста как
неполного или ещё не реализованного.
``PHPUnit\Framework\IncompleteTest``- это интерфейс маркера для обозначения
исключения, возникающего тестовым методом как результат на то, что данный
тестовый метод неполный или в данный момент ещё не реализован.
``PHPUnit\Framework\IncompleteTestError`` — стандартная реализация этого интерфейса.

:numref:`incomplete-and-skipped-tests.incomplete-tests.examples.SampleTest.php`
показывает тестовый класс ``SampleTest``, содержащий один тестовый метод ``testSomething()``.
Вызывая удобный метод ``markTestIncomplete()`` (который автоматически
вызывает исключение ``PHPUnit\Framework\IncompleteTestError``) в тестовом методе, мы отмечаем,
что данный тест является неполным.

.. code-block:: php
    :caption: Маркировка теста как неполного
    :name: incomplete-and-skipped-tests.incomplete-tests.examples.SampleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SampleTest extends TestCase
    {
        public function testSomething()
        {
            // Необязательно: протестируйте здесь что-нибудь, если хотите.
            $this->assertTrue(true, 'This should already work.');

            // Остановиться здесь и отметить, что тест неполный.
            $this->markTestIncomplete(
              'This test has not been implemented yet.'
            );
        }
    }

Неполный тест обозначается ``I`` в выводе исполнителя тестов командной строки
PHPUnit, как показано в следующем примере:

.. code-block:: bash

    $ phpunit --verbose SampleTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    I

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 incomplete test:

    1) SampleTest::testSomething
    This test has not been implemented yet.

    /home/sb/SampleTest.php:12
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 1, Incomplete: 1.

:numref:`incomplete-and-skipped-tests.incomplete-tests.tables.api`
показывает API для маркировки тестов как неполных.

.. rst-class:: table
.. list-table:: API for Incomplete Tests
    :name: incomplete-and-skipped-tests.incomplete-tests.tables.api
    :header-rows: 1

    * - Метод
      - Описание
    * - ``void markTestIncomplete()``
      - Помечает текущий тест как неполный.
    * - ``void markTestIncomplete(string $message)``
      - Помечает текущий тест как неполный, используя ``$message`` в качестве пояснительного сообщения.

.. _incomplete-and-skipped-tests.skipping-tests:

Пропущенные тесты
#################

Не все тесты могут выполняться в любом окружении. Рассмотрим, например,
уровень абстракции базы данных, содержащий несколько драйверов для различных систем
баз данных, которые он поддерживает. Разумеется, тесты для драйвера MySQL могут
выполняться только в том случае, если доступен сервер MySQL.

:numref:`incomplete-and-skipped-tests.skipping-tests.examples.DatabaseTest.php`
демонстрирует тестовый класс ``DatabaseTest``, содержащий один тестовый
метод ``testConnection()``. В шаблонном методе ``setUp()`` тестового класса мы проверяем,
доступно ли расширение MySQLi, и используем метод ``markTestSkipped()``
для пропуска этого теста в противном случае.

.. code-block:: php
    :caption: Пропуск теста
    :name: incomplete-and-skipped-tests.skipping-tests.examples.DatabaseTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DatabaseTest extends TestCase
    {
        protected function setUp()
        {
            if (!extension_loaded('mysqli')) {
                $this->markTestSkipped(
                  'Расширение MySQLi недоступно.'
                );
            }
        }

        public function testConnection()
        {
            // ...
        }
    }

Пропущенный тест обозначается ``S`` в выводе исполнителя тестов командной строки
PHPUnit, как показано в следующем примере:

.. code-block:: bash

    $ phpunit --verbose DatabaseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    S

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 skipped test:

    1) DatabaseTest::testConnection
    The MySQLi extension is not available.

    /home/sb/DatabaseTest.php:9
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 0, Skipped: 1.

:numref:`incomplete-and-skipped-tests.skipped-tests.tables.api`
показывает API пропущенных тестов.

.. rst-class:: table
.. list-table:: API for Skipping Tests
    :name: incomplete-and-skipped-tests.skipped-tests.tables.api
    :header-rows: 1

    * - Метод
      - Описание
    * - ``void markTestSkipped()``
      - Отмечает текущий тест как пропущенный.
    * - ``void markTestSkipped(string $message)``
      - Отмечает текущий тест как пропущенный, используя ``$message`` в качестве пояснительного сообщения.

.. _incomplete-and-skipped-tests.skipping-tests-using-requires:

Пропуск тестов с помощью @requires
##################################

В дополнение к вышеперечисленным методам можно также использовать аннотацию
``@requires``, чтобы предоставить общие предварительные условия для тестового класса.

.. rst-class:: table
.. list-table:: Possible @requires usages
    :name: incomplete-and-skipped-tests.requires.tables.api
    :header-rows: 1

    * - Тип
      - Возможные значения
      - Примеры
      - Дополнительный пример
    * - ``PHP``
      - Любой идентификатор версии PHP
      - @requires PHP 5.3.3
      - @requires PHP 7.1-dev
    * - ``PHPUnit``
      - Любой идентификатор версии PHPUnit
      - @requires PHPUnit 3.6.3
      - @requires PHPUnit 4.6
    * - ``OS``
      - Регулярное выражения для `PHP_OS <http://php.net/manual/en/reserved.constants.php#constant.php-os>`_
      - @requires OS Linux
      - @requires OS WIN32|WINNT
    * - ``OSFAMILY``
      - Любое `семейство ОС <http://php.net/manual/en/reserved.constants.php#constant.php-os-family>`_
      - @requires OSFAMILY Solaris
      - @requires OSFAMILY Windows
    * - ``function``
      - Любой корректный параметр для `function_exists <http://php.net/function_exists>`_
      - @requires function imap_open
      - @requires function ReflectionMethod::setAccessible
    * - ``extension``
      - Имя расширения вместе с необязательным идентификатором версии
      - @requires extension mysqli
      - @requires extension redis 2.2.0

.. code-block:: php
    :caption: Пропуск тестового класса с использованием @requires
    :name: incomplete-and-skipped-tests.skipping-tests.examples.DatabaseClassSkippingTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @requires extension mysqli
     */
    class DatabaseTest extends TestCase
    {
        /**
         * @requires PHP 5.3
         */
        public function testConnection()
        {
            // Тест требует расширения mysqli и PHP >= 5.3
        }

        // ... Все остальные тесты требует расширения mysqli
    }

Если вы используете синтаксис, который не компилируется с определённой версией PHP,
посмотрите на версии от которых зависят тестовые классы в XML-конфигурации (см :ref:`appendixes.configuration.testsuites`)
