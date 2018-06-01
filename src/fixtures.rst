.. _fixtures:

========
Фикстуры
========

Одной из наиболее трудозатратных частей при написании тестов - написание кода
настройки тестового окружения в известное состояние, а затем возврат его в
исходное состояние, когда тест будет завершён. Это известное состояние называется
*фикстурой* теста.

One of the most time-consuming parts of writing tests is writing the
code to set the world up in a known state and then return it to its
original state when the test is complete. This known state is called
the *fixture* of the test.

В разделе :ref:`writing-tests-for-phpunit.examples.StackTest.php` фикстурой
была простой массив, который хранится в переменной ``$stack``.
Однако, в большинстве случаев, фикстура будет более сложной,
чем простой массив, и количество кода, необходимое для её настройки,
будет соответственно расти. Фактическое содержание теста потеряется в шуме
настройки фикстуры. Эта проблема становится хуже, когда вы пишите
несколько тестов с похожими фикстурами. Без помощи от фреймворка тестирования,
нам пришлось дублировать код, который устанавливает фикстуру для каждого
теста, который мы пишем.

PHPUnit поддерживает общий код установки. Перед тем выполнением тестового метода,
будет вызван шаблонный метод ``setUp()``.
``setUp()`` - это там, где вы создаёте объекты, которые вы будете тестировать.
После того, как тестовый метод выполнился, вне зависимости успешно или нет,
вызывается другой шаблонный метод с названием ``tearDown()``. ``tearDown()``
- это там, где вы очищаете протестированные объекты.

В разделе :ref:`writing-tests-for-phpunit.examples.StackTest2.php` мы использовали
отношения между продюсер-потребитель между тестами для совместного использования
фикстур. Это не всегда желательно или даже возможно.
:numref:`fixtures.examples.StackTest.php`
показывает, как мы можем написать тесты ``StackTest`` таким образом, чтобы не сама фикстура повторно использовалась, а код, который создаёт её.
Сначала мы объявляем переменную экземпляра, ``$stack``, которую мы
будем использовать вместо обычной переменной в методе. Затем мы помещаем
создание  массива (``array``) фикстуры в метод ``setUp()``. Наконец, мы удаляем избыточный код
из тестовых методов и используем недавно созданную переменную экземпляра,
``$this->stack``, вместо локальной переменной метода
``$stack`` в методу утверждения ``assertSame()``.

.. code-block:: php
    :caption: Использование setUp() для создания фикстуры
    :name: fixtures.examples.StackTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        protected $stack;

        protected function setUp()
        {
            $this->stack = [];
        }

        public function testEmpty()
        {
            $this->assertTrue(empty($this->stack));
        }

        public function testPush()
        {
            array_push($this->stack, 'foo');
            $this->assertSame('foo', $this->stack[count($this->stack)-1]);
            $this->assertFalse(empty($this->stack));
        }

        public function testPop()
        {
            array_push($this->stack, 'foo');
            $this->assertSame('foo', array_pop($this->stack));
            $this->assertTrue(empty($this->stack));
        }
    }

Шаблонные методы ``setUp()`` and ``tearDown()`` вызываются по одному разу при каждом выполнении тестового метода (и для нового экземпляра)
тестового класса.

Кроме того, вызываются шаблонные методы ``setUpBeforeClass()`` и
``tearDownAfterClass()`` перед тем, как первый тест в тестовом классе будет выполнен,
и после запуска последнего теста тестового класса, соответственно.

Приведённый ниже пример показывает все шаблонные методы, доступные в тестовом классе.

.. code-block:: php
    :caption: Пример, показывающий все доступные шаблонные методы
    :name: fixtures.examples.TemplateMethodsTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class TemplateMethodsTest extends TestCase
    {
        public static function setUpBeforeClass()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function setUp()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function assertPreConditions()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        public function testOne()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            $this->assertTrue(true);
        }

        public function testTwo()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            $this->assertTrue(false);
        }

        protected function assertPostConditions()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function tearDown()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        public static function tearDownAfterClass()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function onNotSuccessfulTest(Exception $e)
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            throw $e;
        }
    }

.. code-block:: bash

    $ phpunit TemplateMethodsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    TemplateMethodsTest::setUpBeforeClass
    TemplateMethodsTest::setUp
    TemplateMethodsTest::assertPreConditions
    TemplateMethodsTest::testOne
    TemplateMethodsTest::assertPostConditions
    TemplateMethodsTest::tearDown
    .TemplateMethodsTest::setUp
    TemplateMethodsTest::assertPreConditions
    TemplateMethodsTest::testTwo
    TemplateMethodsTest::tearDown
    TemplateMethodsTest::onNotSuccessfulTest
    FTemplateMethodsTest::tearDownAfterClass

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) TemplateMethodsTest::testTwo
    Failed asserting that <boolean:false> is true.
    /home/sb/TemplateMethodsTest.php:30

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

.. _fixtures.more-setup-than-teardown:

Больше setUp() чем tearDown()
#############################

``setUp()`` and ``tearDown()`` довольно симметричны
в теории, но не на практике. На практике вам нужно
реализовывать ``tearDown()``, если вы в ``setUp()`` создали внешние ресурсы,
такие как файлы или сокеты.
Если ваш метод ``setUp()`` просто создаёт обычные PHP-объекты, вы
можете вообще игнорировать ``tearDown()``. Однако, если вы
создаёте много объектов в своём ``setUp()``, вам возможно потребуется
использовать ``unset()`` для удаления переменных, указывающие на эти объекты в
в своём ``tearDown()``, чтобы они могли быть очищены сборщиком мусора.
Сборщик мусора объектов тестового класса непредсказуем.

.. _fixtures.variations:

Разновидности
#############

Что произойдёт, если у вас есть два теста с немного различающимися настройками?
Есть два варианта:

-

  Если код ``setUp()`` отличается совсем немного, то
  необходимо перенести код, отличающиеся от ``setUp()``, в тестовый метод.

-

  Если у вас действительно разный ``setUp()``, требуется создать
  другой тестовый класс. Name the class after the difference in
  the setup.

.. _fixtures.sharing-fixture:

Совместное использование фикстур
################################

Есть несколько веских причин для совместного использования фикстур между тестами, но в большинстве
случаев эта необходимость связана с неразрешённой проблемой проектирования.

Хорошим примером фикстуры для совместного использования между тестами может быть
соединение с базой данных: вы подключаетесь к базе данных только один раз и затем повторно используете
это соединение для каждого теста вместо создания нового.
test. Это позволяет сделать ваши тесты быстрее.

:numref:`fixtures.sharing-fixture.examples.DatabaseTest.php`
использует шаблонные методы ``setUpBeforeClass()`` и
``tearDownAfterClass()`` для подключения к базе данных
до выполнения первого теста в тестовом классе и закрытие соединения
с базой данных после запуска последнего теста, соответственно.

.. code-block:: php
    :caption: Совместное использование фикстур тестами в тестовом наборе
    :name: fixtures.sharing-fixture.examples.DatabaseTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DatabaseTest extends TestCase
    {
        protected static $dbh;

        public static function setUpBeforeClass()
        {
            self::$dbh = new PDO('sqlite::memory:');
        }

        public static function tearDownAfterClass()
        {
            self::$dbh = null;
        }
    }

Следует вновь отметить, что совместное использование фикстур между тестами
снижает ценность тестов. Основная проблема проектирования заключается в том,
что объекты сильно связаны между собой. Вы достигнете лучших результатов,
решая эту основную проблему в проектировании, а после напишите тесты,
используя заглушки (stubs) (see :ref:`test-doubles`), а не создавая
зависимости между тестами во время выполнения и игнорируя возможность
улучшения архитектуры.

.. _fixtures.global-state:

Глобальное состояние
####################

`Трудно тестировать код, который использует синглтоны. <http://googletesting.blogspot.com/2008/05/tott-using-dependancy-injection-to.html>`_
То же самое относится и к коду, использующему глобальные переменные. Обычно код,
который вы хотите протестировать, сильно связан с глобальной переменной, и вы не можете
управлять её созданием. Ещё одна проблема заключается в том, что одно изменение в тесте,
использующим глобальную переменную, может сломать другой тест.

В PHP глобальные переменные работают следующим образом:

-

  Глобальная переменная ``$foo = 'bar';`` сохраняется как ``$GLOBALS['foo'] = 'bar';``.

-

  Переменная ``$GLOBALS`` — это так называемая *суперглобальная (super-global)* переменная.

-

  Суперглобальные переменные — это встроенные переменные, доступные во всех областях видимости.

-

  В области видимости функции или метода вы можете получить доступ к ``$foo`` либо напрямую через ``$GLOBALS['foo']`` или используя ``global $foo;``
  для создания локальной переменной в текущей области видимости, ссылающиеся на глобальную переменную.

Помимо глобальных переменных, статические атрибуты классов также являются частью
глобального состояния.

До версии 6, PHPUnit по умолчанию запускал тесты таким образом,
что изменения в глобальных и суперглобальных переменных (``$GLOBALS``,
``$_ENV``, ``$_POST``,
``$_GET``, ``$_COOKIE``,
``$_SERVER``, ``$_FILES``,
``$_REQUEST``) не влияли на другие тесты.

Начиная с версии 6, PHPUnit больше не делает операции резервного копирования и восстановления
глобальных и суперглобальных переменных по умолчанию.
Это можно включить, используя опцию ``--globals-backup``
или настройку ``backupGlobals="true"`` в конфигурационном XML-файле.

Используя опцию ``--static-backup`` или настройку
``backupStaticAttributes="true"`` в конфигурационном
XML-файле, данная изоляция выше может быть расширена до статических атрибутов классов.

.. admonition:: Note

   Операции резервного копирования и восстановления глобальных переменных и статических
   атрибутов классов используют ``serialize()`` и
   ``unserialize()``.

   Объекты некоторых классов (например, ``PDO``) не могут быть
   сериализованы, и операция резервного копирования будет прервана,
   когда подобный объект будет сохраняться, например, в массив  ``$GLOBALS``.

Аннотация ``@backupGlobals``, которая обсуждается в
:ref:`appendixes.annotations.backupGlobals`, может использоваться для
управления операциями резервного копирования и восстановления глобальных переменных.
Кроме этого, вы можете предоставить чёрный список глобальных переменных, которые должны быть
исключены при выполнении операций резервного копирования и восстановления, как показано ниже:

.. code-block:: php

    class MyTest extends TestCase
    {
        protected $backupGlobalsBlacklist = ['globalVariable'];

        // ...
    }

.. admonition:: Note

   Установка свойства ``$backupGlobalsBlacklist`` внутри, например,
   метода``setUp()``, не даст никакого эффекта.

Аннотацию ``@backupStaticAttributes``, обсуждаемая в
:ref:`appendixes.annotations.backupStaticAttributes`, можно использовать
для резервного копирования всех статических значений свойств во всех объявленных классах
перед каждым тестом с последующим их восстановлением.

Она обрабатывает все классы, объявленные в момент запуска теста, а не
только сам тестовый класс. Она применяется только к статическим свойствам класса, а не к статическим переменным внутри функций.

.. admonition:: Note

   Операция ``@backupStaticAttributes`` выполняется перед каждым тестовым методом,
   но только если она включена. Если статическое значение было
   изменено ранее выполненным тестом с отключенным
   ``@backupStaticAttributes``, тогда это значение будет скопировано
   и восстановлено, но не к первоначальному значению по умолчанию.
   PHP не записывает первоначально объявленное значение по умолчанию любой
   статической переменной.

   То же самое относительно и к статическим свойствам классов, которые недавно были
   загружены или объявлены внутри теста. Они не могут быть сброшены к первоначально объявленному значению по умолчанию после теста,
   так как это значение неизвестно. Независимо установленного значения, произойдёт
   утечка в последующие тесты.

   Для модульных тестов рекомендуется явно сбросить значения статических свойств
   в методе теста ``setUp()`` (и в идеале также в методе ``tearDown()``,
   чтобы не повлиять на последующие выполняемые тесты).

Вы можете предоставить чёрный список статических атрибутов, которые должны быть исключены из операций резервного копирования и восстановления:

.. code-block:: php

    class MyTest extends TestCase
    {
        protected $backupStaticAttributesBlacklist = [
            'className' => ['attributeName']
        ];

        // ...
    }

.. admonition:: Note

   Установка свойства ``$backupStaticAttributesBlacklist`` внутри,
   например, метода ``setUp()``, не даст никакого эффекта.


