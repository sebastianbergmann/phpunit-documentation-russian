.. _appendixes.annotations:

=========
Аннотации
=========

Аннотация — специальная форма синтаксических метаданных, которые могут добавлены
в исходный код некоторых языков программирования. Хотя у PHP нет собственной
языковой возможности для аннотирования исходного кода, использование тегов, таких как
``@annotation arguments`` в блоке документации (альтернативное название — докблок), было принято
в сообществе PHP для аннотации кода. В PHP блоки документации
«рефлексивны»: к ним можно получить доступ с помощью
метода API Reflection ``getDocComment()`` на уровне функции,
класса, методе и атрибуте. Такие приложения, как PHPUnit, используют
информацию во время выполнения для настройки их поведения.

.. admonition:: Примечание

   Комментарий документации в PHP должен начинаться с ``/**`` и заканчиваться
   ``*/``. Аннотации в любом другом стиле комментария будут проигнорированы.

В этом приложении представлены все разновидности аннотаций, поддерживаемые PHPUnit.

.. _appendixes.annotations.author:

@author
#######

Аннотация ``@author`` — это псевдоним для аннотации
``@group`` (см. :ref:`appendixes.annotations.group`), позволяющая фильтровать тесты
на основе их авторов.

.. _appendixes.annotations.after:

@after
######

Аннотацию ``@after`` можно использовать для указания методов,
которые должны вызываться после каждого тестового метода в тестовом классе.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @after
         */
        public function tearDownSomeFixtures()
        {
            // ...
        }

        /**
         * @after
         */
        public function tearDownSomeOtherFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.afterClass:

@afterClass
###########

Аннотацию ``@afterClass`` можно использовать для указания
статических методов, которые должны вызываться после того, как все тестовые методы
в тестовом классе были запущены для очистки общих фикстур.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @afterClass
         */
        public static function tearDownSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @afterClass
         */
        public static function tearDownSomeOtherSharedFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.backupGlobals:

@backupGlobals
##############

Операции резервного копирования и восстановления глобальных переменных могут быть полностью
отключены для всех тестов в тестовом классе следующим образом:

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals disabled
     */
    class MyTest extends TestCase
    {
        // ...
    }

Аннотацию ``@backupGlobals`` также можно использовать на уровне
тестового метода. Это позволяет выполнять тонкую настройку операций
резервного копирования и восстановления:

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals disabled
     */
    class MyTest extends TestCase
    {
        /**
         * @backupGlobals enabled
         */
        public function testThatInteractsWithGlobalVariables()
        {
            // ...
        }
    }

.. _appendixes.annotations.backupStaticAttributes:

@backupStaticAttributes
#######################

Аннотацию ``@backupStaticAttributes`` можно использовать для
резервного копирования всех значений статических свойств во всех объявленных классах перед
каждым тестом с последующим их восстановлением. Она может использоваться на уровне тестового класса
или тестового метода:

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupStaticAttributes enabled
     */
    class MyTest extends TestCase
    {
        /**
         * @backupStaticAttributes disabled
         */
        public function testThatInteractsWithStaticAttributes()
        {
            // ...
        }
    }

.. admonition:: Примечание

   Аннотация ``@backupStaticAttributes`` ограничивается внутренне PHP
   и при определённых условиях может привести
   к непреднамеренному сохранению статических значений и утечке памяти
   в последующих тестах.

   См. :ref:`fixtures.global-state` дополнительной информации.

.. _appendixes.annotations.before:

@before
#######

Аннотацию ``@before`` можно использовать для указания методов,
которые должны вызываться перед каждым тестовым методом в тестовом классе.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @before
         */
        public function setupSomeFixtures()
        {
            // ...
        }

        /**
         * @before
         */
        public function setupSomeOtherFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.beforeClass:

@beforeClass
############

Аннотацию ``@beforeClass`` можно использовать для указания
статических методов, которые должны вызываться до выполнения любых тестов в тестовом
классе для настройки общих фикстур.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @beforeClass
         */
        public static function setUpSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @beforeClass
         */
        public static function setUpSomeOtherSharedFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.codeCoverageIgnore:

@codeCoverageIgnore*
####################

Аннотации ``@codeCoverageIgnore``,
``@codeCoverageIgnoreStart`` и
``@codeCoverageIgnoreEnd`` могут использоваться
для исключения строк кода из анализа покрытия.

Для использования см. :ref:`code-coverage-analysis.ignoring-code-blocks`.

.. _appendixes.annotations.covers:

@covers
#######

Аннотация ``@covers`` может использовать в тестовом коде для
указания, какие методы собирается тестировать данный тестовый метод:

.. code-block:: php

    /**
     * @covers BankAccount::getBalance
     */
    public function testBalanceIsInitiallyZero()
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

Если эта аннотация задана, будет учитываться информация о покрытии кода только для указанных методов.

:numref:`appendixes.annotations.covers.tables.annotations` показывает
синтаксис аннотации ``@covers``.

.. rst-class:: table
.. list-table:: Аннотации для указания, какие методы покрываются тестом
    :name: appendixes.annotations.covers.tables.annotations
    :header-rows: 1

    * - Аннотация
      - Описание
    * - ``@covers ClassName::methodName``
      - Указывает, что аннотированный тестовый метод покрывает указанный метод.
    * - ``@covers ClassName``
      - Указывает, что аннотированный тестовый метод покрывает все методы данного класса.
    * - ``@covers ClassName<extended>``
      - Указывает, что аннотированный тестовый метод покрывает все методы заданного класса и его родительских классов или интерфейсов.
    * - ``@covers ClassName::<public>``
      - Указывает, что аннотированный тестовый метод покрывает все общедоступные методы заданного класса.
    * - ``@covers ClassName::<protected>``
      - Указывает, что аннотированный тестовый метод покрывает все защищённые методы заданного класса.
    * - ``@covers ClassName::<private>``
      - Указывает, что аннотированный тестовый метод покрывает все закрытые методы заданного класса.
    * - ``@covers ClassName::<!public>``
      - Указывает, что аннотированный тестовый метод покрывает все не общедоступные методы заданного класса.
    * - ``@covers ClassName::<!protected>``
      - Указывает, что аннотированный тестовый метод покрывает все не защищённые методы заданного класса.
    * - ``@covers ClassName::<!private>``
      - Указывает, что аннотированный тестовый метод покрывает все не закрытые методы заданного класса.
    * - ``@covers ::functionName``
      - Указывает, что аннотированный тестовый метод покрывает указанную глобальную функцию.

.. _appendixes.annotations.coversDefaultClass:

@coversDefaultClass
###################

Аннотацию ``@coversDefaultClass`` можно использовать
для указания пространства имени по умолчанию или класса. Таким образом, длинные имена не нужно
повторно указывать для каждой аннотации ``@covers``. См.
:numref:`appendixes.annotations.examples.CoversDefaultClassTest.php`.

.. code-block:: php
    :caption: Использование @coversDefaultClass для сокращений аннотаций
    :name: appendixes.annotations.examples.CoversDefaultClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @coversDefaultClass \Foo\CoveredClass
     */
    class CoversDefaultClassTest extends TestCase
    {
        /**
         * @covers ::publicMethod
         */
        public function testSomething()
        {
            $o = new Foo\CoveredClass;
            $o->publicMethod();
        }
    }

.. _appendixes.annotations.coversNothing:

@coversNothing
##############

Аннотацию ``@coversNothing`` можно использовать в тестовом
коде для указания, что информация о покрытии кода не должна учитываться
для данного тестового класса.

Это можно использовать для интеграционного тестирования. См.
:ref:`code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php`
для примера.

Данную аннотацию можно использовать на уровне классе или метода и переопределить любые теги ``@covers``.

.. _appendixes.annotations.dataProvider:

@dataProvider
#############

Тестовый метод может принимать произвольное количество аргументов. Эти аргументы должны
быть предоставлены одним или несколькими методами провайдера данных (``provider()`` в
:ref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`).
Используемый метод провайдера данных задаётся с помощью аннотации
``@dataProvider``.

См. :ref:`writing-tests-for-phpunit.data-providers` для получения подробной информации.

.. _appendixes.annotations.depends:

@depends
########

PHPUnit поддерживает объявление явных зависимостей между тестовыми
методами. Такие зависимости не определяют порядок, в котором должны выполняться тестовые методы,
но они позволяют возвращать экземпляр
фикстуры теста продюсером и передавать его зависимым потребителям.
:ref:`writing-tests-for-phpunit.examples.StackTest2.php` показывает,
как использовать аннотацию ``@depends`` для выражения
зависимостей между тестовыми методами.

См. :ref:`writing-tests-for-phpunit.test-dependencies` для подробной информации.

.. _appendixes.annotations.doesNotPerformAssertions:

@doesNotPerformAssertions
#########################

Предотвращает выполнение теста, не выполняющего никаких утверждений, для того чтобы не считать его рискованным.

.. _appendixes.annotations.expectedException:

@expectedException
##################

:ref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php`
показывает, как использовать аннотацию ``@expectedException``
для проверки того, было ли выброшено исключение внутри тестируемого кода.

См. :ref:`writing-tests-for-phpunit.exceptions` для получения подробной информации.

.. _appendixes.annotations.expectedExceptionCode:

@expectedExceptionCode
######################

Аннотация ``@expectedExceptionCode`` в сочетании
с ``@expectedException`` позволяет делать утверждения по
коду ошибке выбрасываемого исключения, таким образом, сужая конкретное исключение.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @expectedException     MyException
         * @expectedExceptionCode 20
         */
        public function testExceptionHasErrorCode20()
        {
            throw new MyException('Сообщение исключения', 20);
        }
    }

Для облегчения тестирования и уменьшения дублирования можно указать
константу класса в
``@expectedExceptionCode``, используя синтаксис
"``@expectedExceptionCode ClassName::CONST``".

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
          * @expectedException     MyException
          * @expectedExceptionCode MyClass::ERRORCODE
          */
        public function testExceptionHasErrorCode20()
        {
          throw new MyException('Сообщение исключения', 20);
        }
    }
    class MyClass
    {
        const ERRORCODE = 20;
    }

.. _appendixes.annotations.expectedExceptionMessage:

@expectedExceptionMessage
#########################

Аннотация ``@expectedExceptionMessage`` работает аналогично
``@expectedExceptionCode``, поскольку она может сделать
утверждение на сообщении исключения.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @expectedException        MyException
         * @expectedExceptionMessage Сообщение исключения
         */
        public function testExceptionHasRightMessage()
        {
            throw new MyException('Сообщение исключения', 20);
        }
    }

Ожидаемое сообщение может быть подстрокой сообщения исключения.
Это может быть полезно, для того чтобы только утверждать, что переданное определённое имя или параметр
встречается в исключении, не фокусируясь на полном совпадении сообщения исключения в тесте.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
         /**
          * @expectedException        MyException
          * @expectedExceptionMessage broken
          */
         public function testExceptionHasRightMessage()
         {
             $param = 'broken';
             throw new MyException('Некорректный параметр "' . $param . '".', 20);
         }
    }

Для облегчения тестирования и уменьшения дублирования можно указать
константу класса в
``@expectedExceptionMessage``, используя синтаксис
"``@expectedExceptionMessage ClassName::CONST``".
Для примера можно посмотреть на :ref:`appendixes.annotations.expectedExceptionCode`.

.. _appendixes.annotations.expectedExceptionMessageRegExp:

@expectedExceptionMessageRegExp
###############################

Ожидаемое сообщение также можно указать в виде регулярного выражения, используя
аннотацию ``@expectedExceptionMessageRegExp``. Это
полезно в ситуациях, когда подстрока не подходит для соответствия
заданному сообщению.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
         /**
          * @expectedException              MyException
          * @expectedExceptionMessageRegExp /Аргумент \d+ не может быть целым ? \w+/u
          */
         public function testExceptionHasRightMessage()
         {
             throw new MyException('Аргумент 2 не может быть целым числом');
         }
    }

.. _appendixes.annotations.group:

@group
######

Тест может быть отмечен как принадлежащий одной или нескольким группам, используя аннотацию
``@group`` следующим образом:

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @group specification
         */
        public function testSomething()
        {
        }

        /**
         * @group regresssion
         * @group bug2204
         */
        public function testSomethingElse()
        {
        }
    }

Аннотацию ``@group`` можно задать для тестового
класса. Затем она будет «унаследована» всеми методами этого тестового класса.

Тесты могут быть выбраны для выполнения на основе групп с использованием
опций командной строки исполнителя тестов ``--group`` и ``--exclude-group``
или используя соответствующие директивы конфигурационного XML-файла.

.. _appendixes.annotations.large:

@large
######

Аннотация ``@large`` — псевдоним для ``@group large``.

Если пакет ``PHP_Invoker`` установлен и включён
строгий режим, большой тест завершится неудачно, если для его выполнения
потребуется более 60 секунд. Этот тайм-аут настраивается через атрибут
``timeoutForLargeTests`` в конфигурационном XML-файле.

.. _appendixes.annotations.medium:

@medium
#######

Аннотация ``@medium`` — псевдоним для ``@group medium``. Средний тест не должен
зависеть от теста, отмеченного как ``@large``.

Если пакет ``PHP_Invoker`` установлен и включён
строгий режим, средний тест завершится неудачно, если для его выполнения
потребуется более 10 секунд. Этот тайм-аут настраивается через атрибут
``timeoutForMediumTests`` в конфигурационном XML-файле.

.. _appendixes.annotations.preserveGlobalState:

@preserveGlobalState
####################

Когда тест запускается в отдельном процессе, PHPUnit попытается
сохранить глобальное состояние из родительского процесса,
сериализуя все глобальные переменные в родительском процессе и десериализуя их
в дочернем процессе. Это может вызвать проблемы, если родительский процесс
содержит глобальные переменные, которые невозможно сериализовать.
Для исправления этого, вы можете запретить PHPUnit сохранять глобальное состояние с помощью аннотации
``@preserveGlobalState``.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         * @preserveGlobalState disabled
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

.. _appendixes.annotations.requires:

@requires
#########

Аннотация ``@requires`` можно использовать для пропуска тестов, когда общие
предварительные условия, такие как версия PHP или установленные расширения, не выполняются.

Полный список возможностей и примеров можно найти в
:ref:`incomplete-and-skipped-tests.requires.tables.api`

.. _appendixes.annotations.runTestsInSeparateProcesses:

@runTestsInSeparateProcesses
############################

Указывает, что все тесты в тестовом классе должны выполняться в отдельном процессе PHP.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @runTestsInSeparateProcesses
     */
    class MyTest extends TestCase
    {
        // ...
    }

*Примечание:* По умолчанию PHPUnit пытается
сохранить глобальное состояние из родительского процесса, сериализуя
все глобальные переменные в родительском процессе и десериализуя их
в дочернем процессе. Это может вызвать проблемы, если родительский процесс
содержит глобальные переменные, которые невозможно сериализовать.
См. :ref:`appendixes.annotations.preserveGlobalState` для получения информации
по изменению этого поведения.

.. _appendixes.annotations.runInSeparateProcess:

@runInSeparateProcess
#####################

Указывает, что тест должен выполняться в отдельном процессе PHP.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

*Примечание:* По умолчанию PHPUnit пытается
сохранить глобальное состояние из родительского процесса, сериализуя
все глобальные переменные в родительском процессе и десериализуя их
в дочернем процессе. Это может вызвать проблемы, если родительский процесс
содержит глобальные переменные, которые невозможно сериализовать.
См. :ref:`appendixes.annotations.preserveGlobalState` для получения информации
по изменению этого поведения.

.. _appendixes.annotations.small:

@small
######

Аннотация ``@small`` — это псевдоним для
``@group small``. Небольшой тест не должен зависеть от теста,
отмеченного как ``@medium`` или ``@large``.

Если пакет ``PHP_Invoker`` установлен и включён
строгий режим, небольшой тест завершится неудачно, если для его выполнения
потребуется более 1 секунды. Этот тайм-аут настраивается через атрибут
``timeoutForSmallTests`` в конфигурационном XML-файле.

.. admonition:: Примечание

   Тесты должны быть явно аннотированы либо ``@small``,
   ``@medium`` или ``@large`` для включения ограничения времени выполнения.

.. _appendixes.annotations.test:

@test
#####

В качестве альтернативы добавления префиксов именам тестовым методам
``test``, вы можете использовать аннотацию ``@test``
в блоке документации метода, чтобы отметить его как тестовый метод.

.. code-block:: php

    /**
     * @test
     */
    public function initialBalanceShouldBe0()
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

.. _appendixes.annotations.testdox:

@testdox
########

Указывает альтернативное описание, используемое при создании предложений для agile-документации.

Аннотацию ``@testdox`` можно применять как к тестовым классам, так и к тестовым методам.

.. code-block:: php

    /**
     * @testdox A bank account
     */
    class BankAccountTest extends TestCase
    {
        /**
         * @testdox has an initial balance of zero
         */
        public function balanceIsInitiallyZero()
        {
            $this->assertSame(0, $this->ba->getBalance());
        }
    }

.. admonition:: Примечание

   До PHPUnit 7.0 (из-за бага в разборе аннотации) использование
   аннотации ``@testdox`` также активировало поведение
   аннотацию ``@test``.

.. code-block:: php

.. _appendixes.annotations.testWith:

@testWith
#########

Вместо реализации метода для использования с ``@dataProvider``,
вы можете определить набор данных, используя аннотацию ``@testWith``.

Набор данных состоит из одного или нескольких элементов. Для определения набора данных
с несколькими элементами, определите каждый элемент на отдельной строке.
Каждый элемент набора данных должен быть массив, определённым в JSON.

См. :ref:`writing-tests-for-phpunit.data-providers` для получения
дополнительной информации о передачи набора данных в тест.

.. code-block:: php

    /**
     * @param string    $input
     * @param int       $expectedLength
     *
     * @testWith        ["test", 4]
     *                  ["longer-string", 13]
     */
    public function testStringLength(string $input, int $expectedLength)
    {
        $this->assertSame($expectedLength, strlen($input));
    }

Представление объекта в JSON будет преобразовано в ассоциативный массив.

.. code-block:: php

    /**
     * @param array     $array
     * @param array     $keys
     *
     * @testWith        [{"day": "monday", "conditions": "sunny"}, ["day", "conditions"]]
     */
    public function testArrayKeys($array, $keys)
    {
        $this->assertSame($keys, array_keys($array));
    }

.. _appendixes.annotations.ticket:

@ticket
#######

Аннотация ``@ticket`` — это псевдоним для аннотации ``@group``
(см. :ref:`appendixes.annotations.group`) и позволяет фильтровать тесты на основе
их идентификатора тикета.

.. _appendixes.annotations.uses:

@uses
#####

Аннотация ``@uses`` указывает код, который будет
выполняться тестом, но не предназначен для покрытия тестом. Хорошим
примером может быть объект значения (value object), который необходим для тестирования единицы (модуля) кода.

.. code-block:: php

    /**
     * @covers BankAccount::deposit
     * @uses   Money
     */
    public function testMoneyCanBeDepositedInAccount()
    {
        // ...
    }

Эта аннотация особенно полезна в режиме строгого режима, когда
непреднамеренно покрытый код приводит тесте к неудаче. См.
:ref:`risky-tests.unintentionally-covered-code` для получения
дополнительной информации о строгом режиме покрытия.
