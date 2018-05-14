.. _writing-tests-for-phpunit:

========================
Написание тестов PHPUnit
========================

:numref:`writing-tests-for-phpunit.examples.StackTest.php` показывает,
как мы можем писать тесты, используя PHPUnit, которые выполняют операции с массивом PHP.
В этом примере представлены основные соглашения и шаги для написания тестов
с помощью PHPUnit:

#.

   Тесты для класса ``Class`` переходят в класс ``ClassTest``.

#.

   ``ClassTest`` наследуется (чаще всего) от ``PHPUnit\Framework\TestCase``.

#.

  Тесты - общедоступные методы, которые называются ``test*``.

   Кроме того, вы можете использовать аннотацию ``@test`` в docblock метода, чтобы пометить его как метод тестирования.

#.

   Внутри тестовых методов используются методы-утверждения, такие как ``assertSame()`` (см. :ref:`appendixes.assertions`), которые
   используется для подтверждения того, соответствует ли фактическое значение ожидаемому значению.

.. code-block:: php
    :caption: Testing array operations with PHPUnit
    :name: writing-tests-for-phpunit.examples.StackTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        public function testPushAndPop()
        {
            $stack = [];
            $this->assertSame(0, count($stack));

            array_push($stack, 'foo');
            $this->assertSame('foo', $stack[count($stack)-1]);
            $this->assertSame(1, count($stack));

            $this->assertSame('foo', array_pop($stack));
            $this->assertSame(0, count($stack));
        }
    }
|
    *Martin Fowler*:

    Всякий раз, когда возникает соблазн что-то распечатать, используя
    ``print``, или написать выражение для отладчика, напишите тест
    вместо этого.

.. _writing-tests-for-phpunit.test-dependencies:

Зависимости тестов
##################

    *Adrian Kuhn et. al.*:

    Модульные тесты (Unit Tests) главным образом пишутся в качестве хорошей практики,
    помогающей разработчикам выявлять и исправлять баги (ошибки), проводить рефакторинг код
    и служить как документация для тестируемого программного модуля.
    Для достижения этих преимуществ модульные тесты
    в идеале должны охватывать все возможные пути в программе. Один модульный тест
    обычно охватывает один конкретный путь в функции или метода. Однако методы тестирования
    необязательно должен быть инкапсулированными и независимыми. Часто
    существуют неявные зависимости между тестовыми методами, скрытые в
    реализации теста.

PHPUnit поддерживает объявление явных зависимостей между тестовыми методами.
Такие зависимости не определяют порядок, в котором должны выполняться тестовые методы,
но они позволяют возвращать экземпляр фикстуры (fixture) теста продюсером (producer)
и передавать его зависимым потребителям (consumers).

-

  Продюсер (producer) - тестовый метод, который выдаёт протестированный модуль в качестве возвращаемого значения.

-

  Потребитель (consumer) - тестовый метод, который зависит от одного или более продюсеров и их возвращаемых значений.

:numref:`writing-tests-for-phpunit.examples.StackTest2.php` показывает,
как использовать аннотацию ``@depends`` для представления зависимостей
между тестовыми методами.

.. code-block:: php
    :caption: Использование аннотации ``@depends`` для представления зависимостей
    :name: writing-tests-for-phpunit.examples.StackTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        public function testEmpty()
        {
            $stack = [];
            $this->assertEmpty($stack);

            return $stack;
        }

        /**
         * @depends testEmpty
         */
        public function testPush(array $stack)
        {
            array_push($stack, 'foo');
            $this->assertSame('foo', $stack[count($stack)-1]);
            $this->assertNotEmpty($stack);

            return $stack;
        }

        /**
         * @depends testPush
         */
        public function testPop(array $stack)
        {
            $this->assertSame('foo', array_pop($stack));
            $this->assertEmpty($stack);
        }
    }

В вышеприведённом примере первый тест, ``testEmpty()``,
создаёт новый массив и утверждает, что он пуст. Затем тест возвращает фикстуру
(fixture) в качестве результата. Второй тест, ``testPush()``,
зависит от ``testEmpty()`` и ему передаётся результат зависимого теста
в качестве аргумента. Наконец, ``testPop()``
зависит от ``testPush()``.

.. admonition:: Note

   Возвращаемое значение, выданное производителем, по умолчанию передаётся потребителям "как есть".
   Это означает, что когда продюсер возвращает объект, ссылка на этот объект передаётся потребителям.
   Вместо ссылки возможна, либо (a) (глубокая) копия через ``@depends clone`` или также (b)
   (обычная неполная) копия (на основе ключевого слова PHP ``clone``) через
   ``@depends shallowClone``.

Чтобы быстро локализовать дефекты, мы хотим сосредоточить наше внимание.
на соответствующих неудачных тестах. Вот почему PHPUnit пропускает выполнение теста,
когда зависимый тест не прошёл. Это помогает локализовать дефекты путём использования
зависимостей между тестами, как это показано
в :numref:`writing-tests-for-phpunit.examples.DependencyFailureTest.php`.

.. code-block:: php
    :caption: Использование зависимостей между тестами
    :name: writing-tests-for-phpunit.examples.DependencyFailureTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DependencyFailureTest extends TestCase
    {
        public function testOne()
        {
            $this->assertTrue(false);
        }

        /**
         * @depends testOne
         */
        public function testTwo()
        {
        }
    }

.. code-block:: bash

    $ phpunit --verbose DependencyFailureTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    FS

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) DependencyFailureTest::testOne
    Failed asserting that false is true.

    /home/sb/DependencyFailureTest.php:6

    There was 1 skipped test:

    1) DependencyFailureTest::testTwo
    This test depends on "DependencyFailureTest::testOne" to pass.

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1, Skipped: 1.

Тест может иметь более одной аннотации ``@depends``.
PHPUnit не изменяет порядок выполнения тестов, поэтому вы должны убедиться,
что зависимости действительно могут быть выполнены до запуска теста.

Тест, содержащий более одно аннотации ``@depends``,
получит фикстуру от первого продюсера в качестве первого аргумента, фикстуру
от второго продюсера во втором аргумент и т.д.
См. :numref:`writing-tests-for-phpunit.examples.MultipleDependencies.php`

.. code-block:: php
    :caption: Тест с несколькими зависимостями
    :name: writing-tests-for-phpunit.examples.MultipleDependencies.php

    <?php
    use PHPUnit\Framework\TestCase;

    class MultipleDependenciesTest extends TestCase
    {
        public function testProducerFirst()
        {
            $this->assertTrue(true);
            return 'first';
        }

        public function testProducerSecond()
        {
            $this->assertTrue(true);
            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         */
        public function testConsumer($a, $b)
        {
            $this->assertSame('first', $a);
            $this->assertSame('second', $b);
        }
    }

.. code-block:: bash

    $ phpunit --verbose MultipleDependenciesTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...

    Time: 0 seconds, Memory: 3.25Mb

    OK (3 tests, 3 assertions)

.. _writing-tests-for-phpunit.data-providers:

Провайдеры данных
#################

Тестовый метод может принимать произвольное количество аргументов. Эти аргументы могут быть
предоставлены одним или несколькими методами провайдеров данных (data provider)
(см. ``additionProvider()`` в :numref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`).
Используемый метод провайдера данных указывается в аннотации ``@dataProvider``.

Метод провайдера данных должен быть объявлен как ``public`` и либо возвращать
массив массивов или объект, реализующий интерфейс ``Iterator``
и выдавать массив для каждого шага итерации. Для каждого массива, являющегося
частью коллекции, будет вызываться тестовый метод с содержимым массива в качестве его аргументов.

.. code-block:: php
    :caption: Использование провайдера данных, который возвращает массив массивов
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionProvider()
        {
            return [
                [0, 0, 0],
                [0, 1, 1],
                [1, 0, 1],
                [1, 1, 3]
            ];
        }
    }

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 (1, 1, 3)
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

При использовании большого количества наборов данных (datasets) полезно указывать для каждого из них строковый ключ,
вместо использования числового ключа по умолчанию.
Вывод станет более подробным, т.к. он будет содержать имя набора данных, который не прошёл тест.

.. code-block:: phpименованными
    :caption: Использование провайдера данных с  наборами данных
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest1.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionProvider()
        {
            return [
                'adding zeros'  => [0, 0, 0],
                'zero plus one' => [0, 1, 1],
                'one plus zero' => [1, 0, 1],
                'one plus one'  => [1, 1, 3]
            ];
        }
    }

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set "one plus one" (1, 1, 3)
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: Использование провайдера данных, который возвращает объект Iterator
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    require 'CsvFileIterator.php';

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionProvider()
        {
            return new CsvFileIterator('data.csv');
        }
    }

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 ('1', '1', '3')
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:11

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: Класс CsvFileIterator
    :name: writing-tests-for-phpunit.data-providers.examples.CsvFileIterator.php

    <?php
    use PHPUnit\Framework\TestCase;

    class CsvFileIterator implements Iterator
    {
        protected $file;
        protected $key = 0;
        protected $current;

        public function __construct($file)
        {
            $this->file = fopen($file, 'r');
        }

        public function __destruct()
        {
            fclose($this->file);
        }

        public function rewind()
        {
            rewind($this->file);
            $this->current = fgetcsv($this->file);
            $this->key = 0;
        }

        public function valid()
        {
            return !feof($this->file);
        }

        public function key()
        {
            return $this->key;
        }

        public function current()
        {
            return $this->current;
        }

        public function next()
        {
            $this->current = fgetcsv($this->file);
            $this->key++;
        }
    }

Когда тест получает входные данные как из метода с ``@dataProvider``,
так и от одного или более методов с аннотацией ``@depends``,
первыми будут приходить аргументы от провайдера данных, а после от зависимых тестов.
Аргументы от зависимых тестов будут одинаковыми для каждого набора данных.
См. :numref:`writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php`

.. code-block:: php
    :caption: Комбинация @depends и @dataProvider в одном тесте
    :name: writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DependencyAndDataProviderComboTest extends TestCase
    {
        public function provider()
        {
            return [['provider1'], ['provider2']];
        }

        public function testProducerFirst()
        {
            $this->assertTrue(true);
            return 'first';
        }

        public function testProducerSecond()
        {
            $this->assertTrue(true);
            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         * @dataProvider provider
         */
        public function testConsumer()
        {
            $this->assertSame(
                ['provider1', 'first', 'second'],
                func_get_args()
            );
        }
    }

.. code-block:: bash

    $ phpunit --verbose DependencyAndDataProviderComboTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 3.50Mb

    There was 1 failure:

    1) DependencyAndDataProviderComboTest::testConsumer with data set #1 ('provider2')
    Failed asserting that two arrays are identical.
    --- Expected
    +++ Actual
    @@ @@
    Array &0 (
    -    0 => 'provider1'
    +    0 => 'provider2'
         1 => 'first'
         2 => 'second'
    )
    /home/sb/DependencyAndDataProviderComboTest.php:32

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: Использование нескольких провайдеров данных для одного теста
      :name: writing-tests-for-phpunit.data-providers.examples.DataTest.php

      <?php
      use PHPUnit\Framework\TestCase;

      class DataTest extends TestCase
      {
          /**
           * @dataProvider additionWithNonNegativeNumbersProvider
           * @dataProvider additionWithNegativeNumbersProvider
           */
          public function testAdd($a, $b, $expected)
          {
              $this->assertSame($expected, $a + $b);
          }

          public function additionWithNonNegativeNumbersProvider()
          {
              return [
                  [0, 1, 1],
                  [1, 0, 1],
                  [1, 1, 3]
              ];
          }

          public function additionWithNegativeNumbersProvider()
          {
              return [
                  [-1, 1, 0],
                  [-1, -1, -2],
                  [1, -1, 0]
              ];
          }
       }

.. code-block:: bash
    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ..F...                                                              6 / 6 (100%)

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 (1, 1, 3)
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:12

    FAILURES!
    Tests: 6, Assertions: 6, Failures: 1.

.. admonition:: Note

   Когда тест зависит от теста, который использует провайдеры данных, зависимый
   тест начнёт выполняться, когда тест, от которого от зависит, успешно выполнится
   как минимум для одного набора данных. Результат теста, который использует провайдеры данных,
   не может быть внедрён в зависимый тест.

.. admonition:: Note

   Все провайдеры данных выполняются как перед вызовом статического метода ``setUpBeforeClass``,
   так и перед первым вызовом метода ``setUp``.
   Из-за этого вы не можете получить доступ к переменным, созданным внутри провайдера данных.
   Это необходимо для того, чтобы PHPUnit мог вычислить общее количество тестов.

.. _writing-tests-for-phpunit.exceptions:

Тестирование исключений
#######################

:numref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php`
показывает, как использовать метод ``expectException()`` для проверки того,
было ли выброшено исключение в тестируемом коде.

.. code-block:: php
    :caption: Использование метода expectException()
    :name: writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExceptionTest extends TestCase
    {
        public function testException()
        {
            $this->expectException(InvalidArgumentException::class);
        }
    }

.. code-block:: bash

    $ phpunit ExceptionTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ExceptionTest::testException
    Failed asserting that exception of type "InvalidArgumentException" is thrown.

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

В дополнение к методу ``expectException()`` существуют методы
``expectExceptionCode()``,
``expectExceptionMessage()`` и
``expectExceptionMessageRegExp()`` для установки ожиданий для
исключений, вызванных тестируемым кодом.

.. admonition:: Note

   Обратите внимание, что метод expectExceptionMessage, утверждает,
   что фактическое сообщение в ``$actual`` содержит ожидаемое сообщение ``$expected``
   без выполнения точного сравнения строк.

Кроме того, вы можете использовать аннотации ``@expectedException``,
``@expectedExceptionCode``,
``@expectedExceptionMessage`` и
``@expectedExceptionMessageRegExp``, чтобы установить
ожидания для исключений, вызванных тестируемым кодом.
:numref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest2.php`
демонстрирует пример использования.

.. code-block:: php
    :caption: Использование аннотации @expectedException
    :name: writing-tests-for-phpunit.exceptions.examples.ExceptionTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExceptionTest extends TestCase
    {
        /**
         * @expectedException InvalidArgumentException
         */
        public function testException()
        {
        }
    }

.. code-block:: bash

    $ phpunit ExceptionTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ExceptionTest::testException
    Failed asserting that exception of type "InvalidArgumentException" is thrown.

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _writing-tests-for-phpunit.errors:

Тестирование ошибок PHP
#######################

По умолчанию PHPUnit преобразует ошибки, предупреждения и уведомления, вызываемые PHP
во время выполнения теста, в исключения.
Использовав эти исключения, вы можете, например, ожидать, что тест вызовет ошибку
PHP, как показано в :numref:`writing-tests-for-phpunit.exceptions.examples.ErrorTest.php`.

.. admonition:: Note

   Конфигурация времени выполнения PHP ``error_reporting`` может
   ограничивать, какие ошибки PHPUnit будет конвертировать в исключения. Если у вас
   возникли проблемы с этой настройкой, убедитесь, что PHP не настроен на подавление
   типов ошибок, которые вы тестируете.

.. code-block:: php
    :caption: Ожидание ошибки PHP в тесте, используя @expectedException
    :name: writing-tests-for-phpunit.exceptions.examples.ErrorTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExpectedErrorTest extends TestCase
    {
        /**
         * @expectedException PHPUnit\Framework\Error\Error
         */
        public function testFailingInclude()
        {
            include 'not_existing_file.php';
        }
    }

.. code-block:: bash

    $ phpunit -d error_reporting=2 ExpectedErrorTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .

    Time: 0 seconds, Memory: 5.25Mb

    OK (1 test, 1 assertion)

Классы ``PHPUnit\Framework\Error\Notice``
``PHPUnit\Framework\Error\Warning`` представляют уведомления и предупреждения PHP
соответственно.

.. admonition:: Note

   Вы должны как можно более конкретно указать исключения при тестировании. Тестирование
   классов, которые являются слишком общими, может привести к нежелательным побочным
   эффектам. Соответственно, тестирование исключения на соответствие классу ``Exception``
   с помощью ``@expectedException`` или ``expectException()`` больше не разрешено.

При тестировании, которое использует функции PHP, вызывающие ошибки, например,
``fopen``, иногда бывает полезно использовать подавление ошибок во время тестирования.
Таким образом, вы можете проверять возвращаемые значения, подавляя уведомления, которые
преобразуется в объекты PHPUnit ``PHPUnit\Framework\Error\Notice``.

.. code-block:: php
    :caption: Тестирование возвращаемых значений в коде, в котором возникают ошибки PHP
    :name: writing-tests-for-phpunit.exceptions.examples.TriggerErrorReturnValue.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ErrorSuppressionTest extends TestCase
    {
        public function testFileWriting() {
            $writer = new FileWriter;

            $this->assertFalse(@$writer->write('/is-not-writeable/file', 'stuff'));
        }
    }

    class FileWriter
    {
        public function write($file, $content) {
            $file = fopen($file, 'w');

            if ($file == false) {
                return false;
            }

            // ...
        }
    }

.. code-block:: bash

    $ phpunit ErrorSuppressionTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .

    Time: 1 seconds, Memory: 5.25Mb

    OK (1 test, 1 assertion)

Без подавления ошибки тест завершится неудачей с сообщением
``fopen(/is-not-writeable/file): failed to open stream: No such file or directory``.

.. _writing-tests-for-phpunit.output:

Тестирования вывода
###################

Иногда вы хотите проверить, что выполнение метода, например,
генерирует ожидаемый вывод (к примеру, через ``echo`` или
``print``). Класс
``PHPUnit\Framework\TestCase`` использует возможность
`буферизации вывода <http://www.php.net/manual/ru/ref.outcontrol.php>`_ PHP
для обеспечения необходимой для этого функциональности.

:numref:`writing-tests-for-phpunit.output.examples.OutputTest.php`
показывает, как использовать метод ``expectOutputString()`` для установки
ожидаемого вывода. Если этот ожидаемый вывод не будет сгенерирован, тест
будет считаться неудачным.

.. code-block:: php
    :caption: Тестирование вывода функции или метода
    :name: writing-tests-for-phpunit.output.examples.OutputTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class OutputTest extends TestCase
    {
        public function testExpectFooActualFoo()
        {
            $this->expectOutputString('foo');
            print 'foo';
        }

        public function testExpectBarActualBaz()
        {
            $this->expectOutputString('bar');
            print 'baz';
        }
    }

.. code-block:: bash

    $ phpunit OutputTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) OutputTest::testExpectBarActualBaz
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

:numref:`writing-tests-for-phpunit.output.tables.api`
показывает доступные методы для тестирования вывода

.. rst-class:: table
.. list-table:: Методы для тестирования вывода
    :name: writing-tests-for-phpunit.output.tables.api
    :header-rows: 1

    * - Метод
      - Описание
    * - ``void expectOutputRegex(string $regularExpression)``
      - Установить ожидание, что вывод соответствует регулярному выражению ``$regularExpression``.
    * - ``void expectOutputString(string $expectedString)``
      - Установить ожидание, что вывод соответствует строке ``$expectedString``.
    * - ``bool setOutputCallback(callable $callback)``
      - Устанавливает функцию обратного вызова, используемую, например, для нормализации фактического вывода.
    * - ``string getActualOutput()``
      - Получить фактический вывод.

.. admonition:: Note

   Тест, который генерирует вывод, не будет работать в строгом режиме.

.. _writing-tests-for-phpunit.error-output:

Вывод ошибки
############

Всякий раз, когда тест терпит неудачу, PHPUnit изо всех сил пытается предоставить вам
максимально возможный контекст, который может помочь выявить проблему.

.. code-block:: php
    :caption: Вывод ошибки, сгенерированный при неудачном сравнении массива
    :name: writing-tests-for-phpunit.error-output.examples.ArrayDiffTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayDiffTest extends TestCase
    {
        public function testEquality()
        {
            $this->assertSame(
                [1, 2,  3, 4, 5, 6],
                [1, 2, 33, 4, 5, 6]
            );
        }
    }

.. code-block:: bash

    $ phpunit ArrayDiffTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayDiffTest::testEquality
    Failed asserting that two arrays are identical.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 1
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

В этом примере только одно из значений массива отличается, а остальные значения показаны,
чтобы предоставить контекст, где произошла ошибка.

Когда сгенерированный вывод будет длинным для чтения, PHPUnit разделит его
и предоставит несколько строк контекста вокруг каждой разницы.

.. code-block:: php
    :caption: Вывод ошибки при неудачном сравнении длинного массива
    :name: writing-tests-for-phpunit.error-output.examples.LongArrayDiffTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class LongArrayDiffTest extends TestCase
    {
        public function testEquality()
        {
            $this->assertSame(
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2,  3, 4, 5, 6],
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2, 33, 4, 5, 6]
            );
        }
    }

.. code-block:: bash

    $ phpunit LongArrayDiffTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LongArrayDiffTest::testEquality
    Failed asserting that two arrays are identical.
    --- Expected
    +++ Actual
    @@ @@
         11 => 0
         12 => 1
         13 => 2
    -    14 => 3
    +    14 => 33
         15 => 4
         16 => 5
         17 => 6
     )

    /home/sb/LongArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _writing-tests-for-phpunit.error-output.edge-cases:

Крайние случаи
==============

Когда сравнение терпит неудачу, PHPUnit создаёт текстовые представления
входных значений и сравнивает их. Из-за этой реализации сравнение изменений (diff)
может показать больше проблем, чем существует на самом деле.

Это происходит только при использовании assertEquals или 'слабых' ('weak') функций
сравнения массивов или объектов.

.. code-block:: php
    :caption: Крайний случай в генерации сравнения при использовании слабого сравнения
    :name: writing-tests-for-phpunit.error-output.edge-cases.examples.ArrayWeakComparisonTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayWeakComparisonTest extends TestCase
    {
        public function testEquality()
        {
            $this->assertEquals(
                [1, 2, 3, 4, 5, 6],
                ['1', 2, 33, 4, 5, 6]
            );
        }
    }

.. code-block:: bash

    $ phpunit ArrayWeakComparisonTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayWeakComparisonTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
    -    0 => 1
    +    0 => '1'
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayWeakComparisonTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

В этом примере сообщается о различии в первом индексе между
``1`` и ``'1'``,
хотя assertEquals считает, что эти значения совпадают.


