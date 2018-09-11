

.. _appendixes.assertions:

===========
Утверждения
===========

В этом приложении перечислены различные доступные методы утверждения.

.. _appendixes.assertions.static-vs-non-static-usage-of-assertion-methods:

Статическое в сравнении с нестатическим использованием методов утверждения
##########################################################################

Утверждения PHPUnit реализованы в ``PHPUnit\Framework\Assert``.
``PHPUnit\Framework\TestCase`` наследуется от ``PHPUnit\Framework\Assert``.

Методы утверждения объявляются статическими и могут быть вызваны
из любого контекста, например ``PHPUnit\Framework\Assert::assertTrue()``,
или используя, например, ``$this->assertTrue()`` или ``self::assertTrue()``,
в классе, наследующий ``PHPUnit\Framework\TestCase``.

Фактически, вы даже можете использовать глобальные функции-обёртки, такие как ``assertTrue()``
в любом контексте (включая классы, наследующие ``PHPUnit\Framework\TestCase``),
когда вы (вручную) включаете файл исходного кода :file:`src/Framework/Assert/Functions.php`,
который поставляется с PHPUnit.

Часто задаваемый вопрос, особенно от разработчиков, впервые работающие с PHPUnit,
является ли использование ``$this->assertTrue()`` или ``self::assertTrue()``, например,
«правильным путём» для вызова утверждения. Короткий ответ: нет правильного пути.
И нет также неправильного пути. Это вопрос личных предпочтений.

Для большинства людей «кажется правильным» использовать ``$this->assertTrue()``
потому что тестовый метод вызывается в контексте тестового объекта. Тот факт, что
методы утверждения объявлены статическими позволяет (повторно) использовать
их вне области видимости тестового объекта. Наконец, глобальные функции-обёртки
позволяют разработчикам вводить меньше символов (``assertTrue()`` вместо
``$this->assertTrue()`` или ``self::assertTrue()``).

.. _appendixes.assertions.assertArrayHasKey:

assertArrayHasKey()
###################

``assertArrayHasKey(mixed $key, array $array[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$array`` не имеет ``$key``.

``assertArrayNotHasKey()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertArrayHasKey()
    :name: appendixes.assertions.assertArrayHasKey.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayHasKeyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertArrayHasKey('foo', ['bar' => 'baz']);
        }
    }

.. code-block:: bash

    $ phpunit ArrayHasKeyTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ArrayHasKeyTest::testFailure
    Failed asserting that an array has the key 'foo'.

    /home/sb/ArrayHasKeyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasAttribute:

assertClassHasAttribute()
#########################

``assertClassHasAttribute(string $attributeName, string $className[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$className::attributeName`` не существует.

``assertClassNotHasAttribute()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertClassHasAttribute()
    :name: appendixes.assertions.assertClassHasAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ClassHasAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasAttribute('foo', stdClass::class);
        }
    }

.. code-block:: bash

    $ phpunit ClassHasAttributeTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasAttributeTest::testFailure
    Failed asserting that class "stdClass" has attribute "foo".

    /home/sb/ClassHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertArraySubset:

assertArraySubset()
###################

``assertArraySubset(array $subset, array $array[, bool $strict = false, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$array`` не содержит ``$subset``.

``$strict`` — флаг, используемый для сравнения идентичности объектов внутри массивов.

.. code-block:: php
    :caption: Использование assertArraySubset()
    :name: appendixes.assertions.assertArraySubset.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArraySubsetTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertArraySubset(['config' => ['key-a', 'key-b']], ['config' => ['key-a']]);
        }
    }

.. code-block:: bash

    $ phpunit ArraySubsetTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) Epilog\EpilogTest::testNoFollowOption
    Failed asserting that an array has the subset Array &0 (
        'config' => Array &1 (
            0 => 'key-a'
            1 => 'key-b'
        )
    ).

    /home/sb/ArraySubsetTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasStaticAttribute:

assertClassHasStaticAttribute()
###############################

``assertClassHasStaticAttribute(string $attributeName, string $className[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$className::attributeName`` не существует.

``assertClassNotHasStaticAttribute()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertClassHasStaticAttribute()
    :name: appendixes.assertions.assertClassHasStaticAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ClassHasStaticAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasStaticAttribute('foo', stdClass::class);
        }
    }

.. code-block:: bash

    $ phpunit ClassHasStaticAttributeTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasStaticAttributeTest::testFailure
    Failed asserting that class "stdClass" has static attribute "foo".

    /home/sb/ClassHasStaticAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContains:

assertContains()
################

``assertContains(mixed $needle, Iterator|array $haystack[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$needle`` не является элементом в ``$haystack``.

``assertNotContains()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

``assertAttributeContains()`` и ``assertAttributeNotContains()`` — удобные обёртки, которые используют общедоступный (``public``), защищённый (``protected``) или закрытый (``private``) атрибут класса или объекта в качестве параметра haystack.

.. code-block:: php
    :caption: Использование assertContains()
    :name: appendixes.assertions.assertContains.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains(4, [1, 2, 3]);
        }
    }

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that an array contains 4.

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertContains(string $needle, string $haystack[, string $message = '', boolean $ignoreCase = false])``

Сообщает об ошибке, определённой в ``$message``, если ``$needle`` не является подстрокой ``$haystack``.

Если ``$ignoreCase`` равен ``true``, тест будет нечувствителен к регистру.

.. code-block:: php
    :caption: Использование assertContains()
    :name: appendixes.assertions.assertContains.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains('baz', 'foobar');
        }
    }

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'foobar' contains "baz".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. code-block:: php
    :caption: Использование assertContains() с $ignoreCase
    :name: appendixes.assertions.assertContains.example3

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains('foo', 'FooBar');
        }

        public function testOK()
        {
            $this->assertContains('foo', 'FooBar', '', true);
        }
    }

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F.

    Time: 0 seconds, Memory: 2.75Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'FooBar' contains "foo".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertContainsOnly:

assertContainsOnly()
####################

``assertContainsOnly(string $type, Iterator|array $haystack[, boolean $isNativeType = null, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$haystack`` содержит не только переменные типа ``$type``.

``$isNativeType`` — флаг, используемый для указания, является ли ``$type`` встроенным в PHP или нет.

``assertNotContainsOnly()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

``assertAttributeContainsOnly()`` и ``assertAttributeNotContainsOnly()`` — удобные обёртки, которые используют общедоступный (``public``), защищённый (``protected``) или закрытый (``private``) атрибут класса или объекта в качестве параметра haystack.

.. code-block:: php
    :caption: Использование assertContainsOnly()
    :name: appendixes.assertions.assertContainsOnly.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsOnlyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnly('string', ['1', '2', 3]);
        }
    }

.. code-block:: bash

    $ phpunit ContainsOnlyTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyTest::testFailure
    Failed asserting that Array (
        0 => '1'
        1 => '2'
        2 => 3
    ) contains only values of type "string".

    /home/sb/ContainsOnlyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContainsOnlyInstancesOf:

assertContainsOnlyInstancesOf()
###############################

``assertContainsOnlyInstancesOf(string $classname, Traversable|array $haystack[, string $message = ''])``

Сообщает об ошибке, определённой в  ``$message``, если ``$haystack`` содержит не только экземпляры класса ``$classname``.

.. code-block:: php
    :caption: Использование assertContainsOnlyInstancesOf()
    :name: appendixes.assertions.assertContainsOnlyInstancesOf.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsOnlyInstancesOfTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnlyInstancesOf(
                Foo::class,
                [new Foo, new Bar, new Foo]
            );
        }
    }

.. code-block:: bash

    $ phpunit ContainsOnlyInstancesOfTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyInstancesOfTest::testFailure
    Failed asserting that Array ([0]=> Bar Object(...)) is an instance of class "Foo".

    /home/sb/ContainsOnlyInstancesOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertCount:

assertCount()
#############

``assertCount($expectedCount, $haystack[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если количество элементов в ``$haystack`` не равно ``$expectedCount``.

``assertNotCount()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertCount()
    :name: appendixes.assertions.assertCount.example

    <?php
    use PHPUnit\Framework\TestCase;

    class CountTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertCount(0, ['foo']);
        }
    }

.. code-block:: bash

    $ phpunit CountTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) CountTest::testFailure
    Failed asserting that actual size 1 matches expected size 0.

    /home/sb/CountTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryExists:

assertDirectoryExists()
#######################

``assertDirectoryExists(string $directory[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если каталог, указанный ``$directory``, не существует.

``assertDirectoryNotExists()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertDirectoryExists()
    :name: appendixes.assertions.assertDirectoryExists.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryExistsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryExists('/path/to/directory');
        }
    }

.. code-block:: bash

    $ phpunit DirectoryExistsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryExistsTest::testFailure
    Failed asserting that directory "/path/to/directory" exists.

    /home/sb/DirectoryExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsReadable:

assertDirectoryIsReadable()
###########################

``assertDirectoryIsReadable(string $directory[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если каталог, указанный ``$directory``, не является каталогом или не доступен для чтения.

``assertDirectoryNotIsReadable()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertDirectoryIsReadable()
    :name: appendixes.assertions.assertDirectoryIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryIsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryIsReadable('/path/to/directory');
        }
    }

.. code-block:: bash

    $ phpunit DirectoryIsReadableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsReadableTest::testFailure
    Failed asserting that "/path/to/directory" is readable.

    /home/sb/DirectoryIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsWritable:

assertDirectoryIsWritable()
###########################

``assertDirectoryIsWritable(string $directory[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если каталог, указанный ``$directory``, не является каталогом или не доступен для записи.

``assertDirectoryNotIsWritable()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertDirectoryIsWritable()
    :name: appendixes.assertions.assertDirectoryIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryIsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryIsWritable('/path/to/directory');
        }
    }

.. code-block:: bash

    $ phpunit DirectoryIsWritableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsWritableTest::testFailure
    Failed asserting that "/path/to/directory" is writable.

    /home/sb/DirectoryIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEmpty:

assertEmpty()
#############

``assertEmpty(mixed $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$actual`` не является пустым.

``assertNotEmpty()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

``assertAttributeEmpty()`` и ``assertAttributeNotEmpty()`` — удобные обёртки, которые могут применяться к общедоступному (``public``), защищённому (``protected``) или закрытому (``private``) атрибуту класса или объекта.

.. code-block:: php
    :caption: Использование assertEmpty()
    :name: appendixes.assertions.assertEmpty.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EmptyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEmpty(['foo']);
        }
    }

.. code-block:: bash

    $ phpunit EmptyTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) EmptyTest::testFailure
    Failed asserting that an array is empty.

    /home/sb/EmptyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEqualXMLStructure:

assertEqualXMLStructure()
#########################

``assertEqualXMLStructure(DOMElement $expectedElement, DOMElement $actualElement[, boolean $checkAttributes = false, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если XML-структура объекта DOMElement в ``$actualElement`` не равна XML-структуре объекта DOMElement в ``$expectedElement``.

.. code-block:: php
    :caption: Использование assertEqualXMLStructure()
    :name: appendixes.assertions.assertEqualXMLStructure.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualXMLStructureTest extends TestCase
    {
        public function testFailureWithDifferentNodeNames()
        {
            $expected = new DOMElement('foo');
            $actual = new DOMElement('bar');

            $this->assertEqualXMLStructure($expected, $actual);
        }

        public function testFailureWithDifferentNodeAttributes()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo bar="true" />');

            $actual = new DOMDocument;
            $actual->loadXML('<foo/>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild, true
            );
        }

        public function testFailureWithDifferentChildrenCount()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><bar/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }

        public function testFailureWithDifferentChildren()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><baz/><baz/><baz/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }
    }

.. code-block:: bash

    $ phpunit EqualXMLStructureTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    FFFF

    Time: 0 seconds, Memory: 5.75Mb

    There were 4 failures:

    1) EqualXMLStructureTest::testFailureWithDifferentNodeNames
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'foo'
    +'bar'

    /home/sb/EqualXMLStructureTest.php:9

    2) EqualXMLStructureTest::testFailureWithDifferentNodeAttributes
    Number of attributes on node "foo" does not match
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualXMLStructureTest.php:22

    3) EqualXMLStructureTest::testFailureWithDifferentChildrenCount
    Number of child nodes of "foo" differs
    Failed asserting that 1 matches expected 3.

    /home/sb/EqualXMLStructureTest.php:35

    4) EqualXMLStructureTest::testFailureWithDifferentChildren
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualXMLStructureTest.php:48

    FAILURES!
    Tests: 4, Assertions: 8, Failures: 4.

.. _appendixes.assertions.assertEquals:

assertEquals()
##############

``assertEquals(mixed $expected, mixed $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если две переменные ``$expected`` и ``$actual`` не равны.

``assertNotEquals()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

``assertAttributeEquals()`` and ``assertAttributeNotEquals()`` — удобные обёртки, которые используют общедоступный (``public``), защищённый (``protected``) или закрытый (``private``) атрибут класса или объекта в качестве фактического значения.

.. code-block:: php
    :caption: Использование assertEquals()
    :name: appendixes.assertions.assertEquals.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(1, 0);
        }

        public function testFailure2()
        {
            $this->assertEquals('bar', 'baz');
        }

        public function testFailure3()
        {
            $this->assertEquals("foo\nbar\nbaz\n", "foo\nbah\nbaz\n");
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    FFF

    Time: 0 seconds, Memory: 5.25Mb

    There were 3 failures:

    1) EqualsTest::testFailure
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualsTest.php:6

    2) EqualsTest::testFailure2
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualsTest.php:11

    3) EqualsTest::testFailure3
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
     'foo
    -bar
    +bah
     baz
     '

    /home/sb/EqualsTest.php:16

    FAILURES!
    Tests: 3, Assertions: 3, Failures: 3.

См. ниже более конкретные сравнения, используемые для определённых типов ``$expected`` и ``$actual``.

``assertEquals(float $expected, float $actual[, string $message = '', float $delta = 0])``

Сообщает об ошибке, определённой в ``$message``, если абсолютная разница между двумя числами с плавающей точкой ``$expected`` и ``$actual`` больше, чем ``$delta``. Если абсолютная разница между двумя числами с плавающей точкой ``$expected`` и ``$actual`` меньше  *или равно* ``$delta``,то утверждение пройдёт успешно.

Прочитайте «`What Every Computer Scientist Should Know About Floating-Point Arithmetic <http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html>`_» для понимания, зачем требуется ``$delta``.

.. code-block:: php
    :caption: Использование assertEquals() с числом с плавающей точкой
    :name: appendixes.assertions.assertEquals.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testSuccess()
        {
            $this->assertEquals(1.0, 1.1, '', 0.1);
        }

        public function testFailure()
        {
            $this->assertEquals(1.0, 1.1);
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that 1.1 matches expected 1.0.

    /home/sb/EqualsTest.php:11

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

``assertEquals(DOMDocument $expected, DOMDocument $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если каноническая форма XML-документов, представленная двумя объектами DOMDocument ``$expected`` и ``$actual``, не равна.

.. code-block:: php
    :caption: Использование assertEquals() с объектами DOMDocument
    :name: appendixes.assertions.assertEquals.example3

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<bar><foo/></bar>');

            $this->assertEquals($expected, $actual);
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
    -<foo>
    -  <bar/>
    -</foo>
    +<bar>
    +  <foo/>
    +</bar>

    /home/sb/EqualsTest.php:12

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(object $expected, object $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если два объекта ``$expected`` и ``$actual`` не имеют одинаковых значений атрибутов.

.. code-block:: php
    :caption: Использование assertEquals() с объектами
    :name: appendixes.assertions.assertEquals.example4

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $expected = new stdClass;
            $expected->foo = 'foo';
            $expected->bar = 'bar';

            $actual = new stdClass;
            $actual->foo = 'bar';
            $actual->baz = 'bar';

            $this->assertEquals($expected, $actual);
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
    -    'foo' => 'foo'
    -    'bar' => 'bar'
    +    'foo' => 'bar'
    +    'baz' => 'bar'
     )

    /home/sb/EqualsTest.php:14

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(array $expected, array $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если два массива ``$expected`` и ``$actual`` не равны.

.. code-block:: php
    :caption: Использование assertEquals() с массивом
    :name: appendixes.assertions.assertEquals.example5

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(['a', 'b', 'c'], ['a', 'c', 'd']);
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 'a'
    -    1 => 'b'
    -    2 => 'c'
    +    1 => 'c'
    +    2 => 'd'
     )

    /home/sb/EqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFalse:

assertFalse()
#############

``assertFalse(bool $condition[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$condition`` равняется ``true``.

``assertNotFalse()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertFalse()
    :name: appendixes.assertions.assertFalse.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FalseTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFalse(true);
        }
    }

.. code-block:: bash

    $ phpunit FalseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) FalseTest::testFailure
    Failed asserting that true is false.

    /home/sb/FalseTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileEquals:

assertFileEquals()
##################

``assertFileEquals(string $expected, string $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если файл, указанный в ``$expected``, не имеет того же содержимого, что и файл, переданный в ``$actual``.

``assertFileNotEquals()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertFileEquals()
    :name: appendixes.assertions.assertFileEquals.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileEqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileEquals('/home/sb/expected', '/home/sb/actual');
        }
    }

.. code-block:: bash

    $ phpunit FileEqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) FileEqualsTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    +'actual
     '

    /home/sb/FileEqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertFileExists:

assertFileExists()
##################

``assertFileExists(string $filename[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если файл, указанный в ``$filename``, не существует.

``assertFileNotExists()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertFileExists()
    :name: appendixes.assertions.assertFileExists.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileExistsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileExists('/path/to/file');
        }
    }

.. code-block:: bash

    $ phpunit FileExistsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileExistsTest::testFailure
    Failed asserting that file "/path/to/file" exists.

    /home/sb/FileExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsReadable:

assertFileIsReadable()
######################

``assertFileIsReadable(string $filename[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если файл, указанный в ``$filename``, не является файлом или не доступен для чтения.

``assertFileNotIsReadable()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertFileIsReadable()
    :name: appendixes.assertions.assertFileIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileIsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileIsReadable('/path/to/file');
        }
    }

.. code-block:: bash

    $ phpunit FileIsReadableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsReadableTest::testFailure
    Failed asserting that "/path/to/file" is readable.

    /home/sb/FileIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsWritable:

assertFileIsWritable()
######################

``assertFileIsWritable(string $filename[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если файл, указанный в ``$filename``, не является файлом или не доступен для записи.

``assertFileNotIsWritable()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertFileIsWritable()
    :name: appendixes.assertions.assertFileIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileIsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileIsWritable('/path/to/file');
        }
    }

.. code-block:: bash

    $ phpunit FileIsWritableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsWritableTest::testFailure
    Failed asserting that "/path/to/file" is writable.

    /home/sb/FileIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThan:

assertGreaterThan()
###################

``assertGreaterThan(mixed $expected, mixed $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если значение ``$actual`` не превышает значение ``$expected``.

``assertAttributeGreaterThan()`` - удобная обёртка, которая использует общедоступный (``public``), защищённый (``protected``) или закрытый (``private``) атрибут класса или объекта в качестве фактического значения.

.. code-block:: php
    :caption: Использование assertGreaterThan()
    :name: appendixes.assertions.assertGreaterThan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class GreaterThanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThan(2, 1);
        }
    }

.. code-block:: bash

    $ phpunit GreaterThanTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) GreaterThanTest::testFailure
    Failed asserting that 1 is greater than 2.

    /home/sb/GreaterThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThanOrEqual:

assertGreaterThanOrEqual()
##########################

``assertGreaterThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если значение ``$actual`` не больше или равно значению ``$expected``.

``assertAttributeGreaterThanOrEqual()`` - удобная обёртка, которая использует общедоступный (``public``), защищённый (``protected``) или закрытый (``private``) атрибут класса или объекта в качестве фактического значения.

.. code-block:: php
    :caption: Использование assertGreaterThanOrEqual()
    :name: appendixes.assertions.assertGreaterThanOrEqual.example

    <?php
    use PHPUnit\Framework\TestCase;

    class GreatThanOrEqualTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThanOrEqual(2, 1);
        }
    }

.. code-block:: bash

    $ phpunit GreaterThanOrEqualTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) GreatThanOrEqualTest::testFailure
    Failed asserting that 1 is equal to 2 or is greater than 2.

    /home/sb/GreaterThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertInfinite:

assertInfinite()
################

``assertInfinite(mixed $variable[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$variable`` не является ``INF``.

``assertFinite()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertInfinite()
    :name: appendixes.assertions.assertInfinite.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InfiniteTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInfinite(1);
        }
    }

.. code-block:: bash

    $ phpunit InfiniteTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InfiniteTest::testFailure
    Failed asserting that 1 is infinite.

    /home/sb/InfiniteTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInstanceOf:

assertInstanceOf()
##################

``assertInstanceOf($expected, $actual[, $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$actual`` не является экземпляром ``$expected``.

``assertNotInstanceOf()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

``assertAttributeInstanceOf()`` и ``assertAttributeNotInstanceOf()`` - удобные обёртки, которые могут применяться к общедоступному (``public``), защищённому (``protected``) или закрытому (``private``) атрибуту класса или объекта.

.. code-block:: php
    :caption: Использование assertInstanceOf()
    :name: appendixes.assertions.assertInstanceOf.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InstanceOfTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInstanceOf(RuntimeException::class, new Exception);
        }
    }

.. code-block:: bash

    $ phpunit InstanceOfTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InstanceOfTest::testFailure
    Failed asserting that Exception Object (...) is an instance of class "RuntimeException".

    /home/sb/InstanceOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInternalType:

assertInternalType()
####################

``assertInternalType($expected, $actual[, $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$actual`` не относится к типу, указанному в ``$expected``.

``assertNotInternalType()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

``assertAttributeInternalType()`` и ``assertAttributeNotInternalType()`` — удобные обёртки, которые могут применяться к общедоступному (``public``), защищённому (``protected``) или закрытому (``private``) атрибуту класса или объекта.

.. code-block:: php
    :caption: Использование assertInternalType()
    :name: appendixes.assertions.assertInternalType.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InternalTypeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInternalType('string', 42);
        }
    }

.. code-block:: bash

    $ phpunit InternalTypeTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InternalTypeTest::testFailure
    Failed asserting that 42 is of type "string".

    /home/sb/InternalTypeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsReadable:

assertIsReadable()
##################

``assertIsReadable(string $filename[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если файл или каталог, указанный в ``$filename``, не доступен для чтения.

``assertNotIsReadable()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertIsReadable()
    :name: appendixes.assertions.assertIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsReadable('/path/to/unreadable');
        }
    }

.. code-block:: bash

    $ phpunit IsReadableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsReadableTest::testFailure
    Failed asserting that "/path/to/unreadable" is readable.

    /home/sb/IsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsWritable:

assertIsWritable()
##################

``assertIsWritable(string $filename[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если файл или каталог, указанный в ``$filename``, не доступен для записи.

``assertNotIsWritable()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertIsWritable()
    :name: appendixes.assertions.assertIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsWritable('/path/to/unwritable');
        }
    }

.. code-block:: bash

    $ phpunit IsWritableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsWritableTest::testFailure
    Failed asserting that "/path/to/unwritable" is writable.

    /home/sb/IsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertJsonFileEqualsJsonFile:

assertJsonFileEqualsJsonFile()
##############################

``assertJsonFileEqualsJsonFile(mixed $expectedFile, mixed $actualFile[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если значение ``$actualFile`` не соответствует значению
``$expectedFile``.

.. code-block:: php
    :caption: Использование assertJsonFileEqualsJsonFile()
    :name: appendixes.assertions.assertJsonFileEqualsJsonFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonFileEqualsJsonFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonFileEqualsJsonFile(
              'path/to/fixture/file', 'path/to/actual/file');
        }
    }

.. code-block:: bash

    $ phpunit JsonFileEqualsJsonFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonFileEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"Tux"}' matches JSON string "["Mascott", "Tux", "OS", "Linux"]".

    /home/sb/JsonFileEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonFile:

assertJsonStringEqualsJsonFile()
################################

``assertJsonStringEqualsJsonFile(mixed $expectedFile, mixed $actualJson[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если значение ``$actualJson`` не соответствует значению
``$expectedFile``.

.. code-block:: php
    :caption: Использование assertJsonStringEqualsJsonFile()
    :name: appendixes.assertions.assertJsonStringEqualsJsonFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonStringEqualsJsonFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonFile(
                'path/to/fixture/file', json_encode(['Mascot' => 'ux'])
            );
        }
    }

.. code-block:: bash

    $ phpunit JsonStringEqualsJsonFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"ux"}' matches JSON string "{"Mascott":"Tux"}".

    /home/sb/JsonStringEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonString:

assertJsonStringEqualsJsonString()
##################################

``assertJsonStringEqualsJsonString(mixed $expectedJson, mixed $actualJson[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если значение ``$actualJson`` не соответствует значению
``$expectedJson``.

.. code-block:: php
    :caption: Использование assertJsonStringEqualsJsonString()
    :name: appendixes.assertions.assertJsonStringEqualsJsonString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonStringEqualsJsonStringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonString(
                json_encode(['Mascot' => 'Tux']),
                json_encode(['Mascot' => 'ux'])
            );
        }
    }

.. code-block:: bash

    $ phpunit JsonStringEqualsJsonStringTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonStringTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
     -    'Mascot' => 'Tux'
     +    'Mascot' => 'ux'
    )

    /home/sb/JsonStringEqualsJsonStringTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertLessThan:

assertLessThan()
################

``assertLessThan(mixed $expected, mixed $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если значение ``$actual`` больше значения ``$expected``.

``assertAttributeLessThan()`` — удобная обёртка, которая использует общедоступный (``public``), защищённый (``protected``) или закрытый (``private``) атрибут класса или объекта в качестве фактического значения.

.. code-block:: php
    :caption: Использование assertLessThan()
    :name: appendixes.assertions.assertLessThan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class LessThanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertLessThan(1, 2);
        }
    }

.. code-block:: bash

    $ phpunit LessThanTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) LessThanTest::testFailure
    Failed asserting that 2 is less than 1.

    /home/sb/LessThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertLessThanOrEqual:

assertLessThanOrEqual()
#######################

``assertLessThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если значение ``$actual`` больше или не равно значению ``$expected``.

``assertAttributeLessThanOrEqual()`` — удобная обёртка, которая использует общедоступный (``public``), защищённый (``protected``) или закрытый (``private``) атрибут класса или объекта в качестве фактического значения.

.. code-block:: php
    :caption: Использование assertLessThanOrEqual()
    :name: appendixes.assertions.assertLessThanOrEqual.example

    <?php
    use PHPUnit\Framework\TestCase;

    class LessThanOrEqualTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertLessThanOrEqual(1, 2);
        }
    }

.. code-block:: bash

    $ phpunit LessThanOrEqualTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LessThanOrEqualTest::testFailure
    Failed asserting that 2 is equal to 1 or is less than 1.

    /home/sb/LessThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertNan:

assertNan()
###########

``assertNan(mixed $variable[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$variable`` не является ``NAN``.

.. code-block:: php
    :caption: Использование assertNan()
    :name: appendixes.assertions.assertNan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertNan(1);
        }
    }

.. code-block:: bash

    $ phpunit NanTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NanTest::testFailure
    Failed asserting that 1 is nan.

    /home/sb/NanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertNull:

assertNull()
############

``assertNull(mixed $variable[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$variable`` не является ``null``.

``assertNotNull()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertNull()
    :name: appendixes.assertions.assertNull.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NullTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertNull('foo');
        }
    }

.. code-block:: bash

    $ phpunit NotNullTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NullTest::testFailure
    Failed asserting that 'foo' is null.

    /home/sb/NotNullTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertObjectHasAttribute:

assertObjectHasAttribute()
##########################

``assertObjectHasAttribute(string $attributeName, object $object[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$object->attributeName`` не существует.

``assertObjectNotHasAttribute()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertObjectHasAttribute()
    :name: appendixes.assertions.assertObjectHasAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ObjectHasAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertObjectHasAttribute('foo', new stdClass);
        }
    }

.. code-block:: bash

    $ phpunit ObjectHasAttributeTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ObjectHasAttributeTest::testFailure
    Failed asserting that object of class "stdClass" has attribute "foo".

    /home/sb/ObjectHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertRegExp:

assertRegExp()
##############

``assertRegExp(string $pattern, string $string[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$string`` не соответствует регулярному выражению ``$pattern``.

``assertNotRegExp()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertRegExp()
    :name: appendixes.assertions.assertRegExp.example

    <?php
    use PHPUnit\Framework\TestCase;

    class RegExpTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertRegExp('/foo/', 'bar');
        }
    }

.. code-block:: bash

    $ phpunit RegExpTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) RegExpTest::testFailure
    Failed asserting that 'bar' matches PCRE pattern "/foo/".

    /home/sb/RegExpTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringMatchesFormat:

assertStringMatchesFormat()
###########################

``assertStringMatchesFormat(string $format, string $string[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$string`` не соответствует строке формата в ``$format``.

``assertStringNotMatchesFormat()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertStringMatchesFormat()
    :name: appendixes.assertions.assertStringMatchesFormat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringMatchesFormatTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormat('%i', 'foo');
        }
    }

.. code-block:: bash

    $ phpunit StringMatchesFormatTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+$/s".

    /home/sb/StringMatchesFormatTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

Строка формата может содержать следующие заполнители:

-

  ``%e``: Представляет разделитель каталогов, например ``/`` в Linux.

-

  ``%s``: Один или несколько чего-либо (символ или пробел), кроме символа конца строки.

-

  ``%S``: Ноль или более чего-либо (символ или пробел), кроме символа конца строки.

-

  ``%a``: Один или несколько чего-либо (символ или пробел), включая символ конца строки.

-

  ``%A``: Ноль или более чего-либо (символ или пробел), включая символ конца строки.

-

  ``%w``: Ноль или более символов пробела.

-

  ``%i``: Целое число со знаком, например ``+3142``, ``-3142``.

-

  ``%d``: Целое число без знака, например ``123456``.

-

  ``%x``: Один или более шестнадцатеричного символа. То есть, символы в диапазоне ``0-9``, ``a-f``, ``A-F``.

-

  ``%f``: Число с плавающей точкой, например: ``3.142``, ``-3.142``, ``3.142E-10``, ``3.142e+10``.

-

  ``%c``: Один символ любого типа.

-

   ``%%``: Буквальный символ процента: ``%``.

.. _appendixes.assertions.assertStringMatchesFormatFile:

assertStringMatchesFormatFile()
###############################

``assertStringMatchesFormatFile(string $formatFile, string $string[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$string`` не соответствует содержимому ``$formatFile``.

``assertStringNotMatchesFormatFile()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertStringMatchesFormatFile()
    :name: appendixes.assertions.assertStringMatchesFormatFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringMatchesFormatFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormatFile('/path/to/expected.txt', 'foo');
        }
    }

.. code-block:: bash

    $ phpunit StringMatchesFormatFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatFileTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+
    $/s".

    /home/sb/StringMatchesFormatFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertSame:

assertSame()
############

``assertSame(mixed $expected, mixed $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если две переменные ``$expected`` и ``$actual`` не имеют одного и того же типа и значения.

``assertNotSame()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

``assertAttributeSame()`` и ``assertAttributeNotSame()`` — удобные обёртки, которые используют общедоступный (``public``), защищённый (``protected``) или закрытый (``private``) атрибут класса или объекта в качестве фактического значения.

.. code-block:: php
    :caption: Использование assertSame()
    :name: appendixes.assertions.assertSame.example

    <?php
    use PHPUnit\Framework\TestCase;

    class SameTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertSame('2204', 2204);
        }
    }

.. code-block:: bash

    $ phpunit SameTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that 2204 is identical to '2204'.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertSame(object $expected, object $actual[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если две переменные ``$expected`` и ``$actual`` ссылаются не на один и тот же объект.

.. code-block:: php
    :caption: Использование assertSame() с объектами
    :name: appendixes.assertions.assertSame.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class SameTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertSame(new stdClass, new stdClass);
        }
    }

.. code-block:: bash

    $ phpunit SameTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that two variables reference the same object.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEndsWith:

assertStringEndsWith()
######################

``assertStringEndsWith(string $suffix, string $string[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$string`` не заканчивается на ``$suffix``.

``assertStringEndsNotWith()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertStringEndsWith()
    :name: appendixes.assertions.assertStringEndsWith.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringEndsWithTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringEndsWith('suffix', 'foo');
        }
    }

.. code-block:: bash

    $ phpunit StringEndsWithTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 1 second, Memory: 5.00Mb

    There was 1 failure:

    1) StringEndsWithTest::testFailure
    Failed asserting that 'foo' ends with "suffix".

    /home/sb/StringEndsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEqualsFile:

assertStringEqualsFile()
########################

``assertStringEqualsFile(string $expectedFile, string $actualString[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если файл, указанный в ``$expectedFile``, не имеет ``$actualString`` в качестве его содержимого.

``assertStringNotEqualsFile()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertStringEqualsFile()
    :name: appendixes.assertions.assertStringEqualsFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringEqualsFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringEqualsFile('/home/sb/expected', 'actual');
        }
    }

.. code-block:: bash

    $ phpunit StringEqualsFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) StringEqualsFileTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    -'
    +'actual'

    /home/sb/StringEqualsFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertStringStartsWith:

assertStringStartsWith()
########################

``assertStringStartsWith(string $prefix, string $string[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$string`` не начинается с ``$prefix``.

``assertStringStartsNotWith()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertStringStartsWith()
    :name: appendixes.assertions.assertStringStartsWith.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringStartsWithTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringStartsWith('prefix', 'foo');
        }
    }

.. code-block:: bash

    $ phpunit StringStartsWithTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringStartsWithTest::testFailure
    Failed asserting that 'foo' starts with "prefix".

    /home/sb/StringStartsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertThat:

assertThat()
############

Более сложные утверждения могут быть сформулированы с использованием
классов ``PHPUnit\Framework\Constraint``. Их можно вычислить
с помощью метода ``assertThat()``.
:numref:`appendixes.assertions.assertThat.example` показывает, как ограничения
``logicalNot()`` и ``equalTo()``
могут использоваться для выражения того же утверждения, что и
``assertNotEquals()``.

``assertThat(mixed $value, PHPUnit\Framework\Constraint $constraint[, $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$value`` не соответствует ``$constraint``.

.. code-block:: php
    :caption: Использование assertThat()
    :name: appendixes.assertions.assertThat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class BiscuitTest extends TestCase
    {
        public function testEquals()
        {
            $theBiscuit = new Biscuit('Ginger');
            $myBiscuit  = new Biscuit('Ginger');

            $this->assertThat(
              $theBiscuit,
              $this->logicalNot(
                $this->equalTo($myBiscuit)
              )
            );
        }
    }

:numref:`appendixes.assertions.assertThat.tables.constraints` показывает
доступные классы ``PHPUnit\Framework\Constraint``.

.. rst-class:: table
.. list-table:: Ограничения
    :name: appendixes.assertions.assertThat.tables.constraints
    :header-rows: 1

    * - Ограничение
      - Описание
    * - ``PHPUnit\Framework\Constraint\Attribute attribute(PHPUnit\Framework\Constraint $constraint, $attributeName)``
      - Ограничение, которое применяет другое ограничение к атрибуту класса или объекта.
    * - ``PHPUnit\Framework\Constraint\IsAnything anything()``
      - Ограничение, принимающее любое входное значение.
    * - ``PHPUnit\Framework\Constraint\ArrayHasKey arrayHasKey(mixed $key)``
      - Ограничение, которое утверждает, что массив (``array``) имеет заданный ключ.
    * - ``PHPUnit\Framework\Constraint\TraversableContains contains(mixed $value)``
      - Ограничение, которое утверждает, что массив (``array``) или объект, реализующий интерфейс ``Iterator``, содержит заданное значение.
    * - ``PHPUnit\Framework\Constraint\TraversableContainsOnly containsOnly(string $type)``
      - Ограничение, которое утверждает, что массив (``array``) или объект, реализующий интерфейс ``Iterator``, содержит только значения заданного типа
    * - ``PHPUnit\Framework\Constraint\TraversableContainsOnly containsOnlyInstancesOf(string $classname)``
      - Ограничение, которое утверждает, что массив (``array``) или объект, реализующий интерфейс ``Iterator``, содержит только экземпляры класса заданного имени класса.
    * - ``PHPUnit\Framework\Constraint\IsEqual equalTo($value, $delta = 0, $maxDepth = 10)``
      - Ограничение, которое проверяет, равно ли одно значение другому.
    * - ``PHPUnit\Framework\Constraint\Attribute attributeEqualTo($attributeName, $value, $delta = 0, $maxDepth = 10)``
      - Ограничение, которое проверяет, равно ли значение атрибуту класса или объекта.
    * - ``PHPUnit\Framework\Constraint\DirectoryExists directoryExists()``
      - Ограничение, которое проверяет, существует ли каталог, для которого он был вычислен.
    * - ``PHPUnit\Framework\Constraint\FileExists fileExists()``
      - Ограничение, которое проверяет, существует ли файл, для которого он был вычислен.
    * - ``PHPUnit\Framework\Constraint\IsReadable isReadable()``
      - Ограничение, которое проверяет, является ли файл доступным для чтения.
    * - ``PHPUnit\Framework\Constraint\IsWritable isWritable()``
      - Ограничение, которое проверяет, является ли файл доступным для записи.
    * - ``PHPUnit\Framework\Constraint\GreaterThan greaterThan(mixed $value)``
      - Ограничение, которое утверждает, что значение больше заданного значения.
    * - ``PHPUnit\Framework\Constraint\Or greaterThanOrEqual(mixed $value)``
      - Ограничение, которое утверждает, что значение больше или равно заданному значению.
    * - ``PHPUnit\Framework\Constraint\ClassHasAttribute classHasAttribute(string $attributeName)``
      - Ограничение, которое утверждает, что класс имеет заданный атрибут.
    * - ``PHPUnit\Framework\Constraint\ClassHasStaticAttribute classHasStaticAttribute(string $attributeName)``
      - Ограничение, которое утверждает, что класс имеет заданный статический атрибут.
    * - ``PHPUnit\Framework\Constraint\ObjectHasAttribute objectHasAttribute(string $attributeName)``
      - Ограничение, которое утверждает, что объект имеет данный атрибут.
    * - ``PHPUnit\Framework\Constraint\IsIdentical identicalTo(mixed $value)``
      - Ограничение, которое утверждает, что одно значение идентично другому.
    * - ``PHPUnit\Framework\Constraint\IsFalse isFalse()``
      - Ограничение, которое утверждает, что значение является``false``.
    * - ``PHPUnit\Framework\Constraint\IsInstanceOf isInstanceOf(string $className)``
      - Ограничение, которое утверждает, что объект является экземпляром заданного класса.
    * - ``PHPUnit\Framework\Constraint\IsNull isNull()``
      - Ограничение, которое утверждает, что значение является ``null``.
    * - ``PHPUnit\Framework\Constraint\IsTrue isTrue()``
      - Ограничение, которое утверждает, что значение является ``true``.
    * - ``PHPUnit\Framework\Constraint\IsType isType(string $type)``
      - Ограничение, которое утверждает, что значение имеет указанный тип.
    * - ``PHPUnit\Framework\Constraint\LessThan lessThan(mixed $value)``
      - Ограничение, которое утверждает, что значение меньше заданного значения.
    * - ``PHPUnit\Framework\Constraint\Or lessThanOrEqual(mixed $value)``
      - Ограничение, которое утверждает, что значение меньше или равно заданному значению.
    * - ``logicalAnd()``
      - Логическое И (AND).
    * - ``logicalNot(PHPUnit\Framework\Constraint $constraint)``
      - Логическое НЕ (NOT).
    * - ``logicalOr()``
      - Логическое ИЛИ (OR).
    * - ``logicalXor()``
      - Логическое исключающее ИЛИ (XOR).
    * - ``PHPUnit\Framework\Constraint\PCREMatch matchesRegularExpression(string $pattern)``
      - Ограничение, которое утверждает, что строка соответствует регулярному выражению.
    * - ``PHPUnit\Framework\Constraint\StringContains stringContains(string $string, bool $case)``
      - Ограничение, которое утверждает, что строка содержит заданную строку.
    * - ``PHPUnit\Framework\Constraint\StringEndsWith stringEndsWith(string $suffix)``
      - Ограничение, которое утверждает, что строка заканчивается заданным суффиксом.
    * - ``PHPUnit\Framework\Constraint\StringStartsWith stringStartsWith(string $prefix)``
      - Ограничение, которое утверждает, что строка начинается с заданного префикса.

.. _appendixes.assertions.assertTrue:

assertTrue()
############

``assertTrue(bool $condition[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если ``$condition`` равно ``false``.

``assertNotTrue()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertTrue()
    :name: appendixes.assertions.assertTrue.example

    <?php
    use PHPUnit\Framework\TestCase;

    class TrueTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertTrue(false);
        }
    }

.. code-block:: bash

    $ phpunit TrueTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) TrueTest::testFailure
    Failed asserting that false is true.

    /home/sb/TrueTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertXmlFileEqualsXmlFile:

assertXmlFileEqualsXmlFile()
############################

``assertXmlFileEqualsXmlFile(string $expectedFile, string $actualFile[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если XML-документ в ``$actualFile`` не равен XML-документу в ``$expectedFile``.

``assertXmlFileNotEqualsXmlFile()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertXmlFileEqualsXmlFile()
    :name: appendixes.assertions.assertXmlFileEqualsXmlFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlFileEqualsXmlFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlFileEqualsXmlFile(
              '/home/sb/expected.xml', '/home/sb/actual.xml');
        }
    }

.. code-block:: bash

    $ phpunit XmlFileEqualsXmlFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlFileEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlFileEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlFile:

assertXmlStringEqualsXmlFile()
##############################

``assertXmlStringEqualsXmlFile(string $expectedFile, string $actualXml[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если XML-документ в ``$actualXml`` не равен XML-документу в ``$expectedFile``.

``assertXmlStringNotEqualsXmlFile()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertXmlStringEqualsXmlFile()
    :name: appendixes.assertions.assertXmlStringEqualsXmlFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlStringEqualsXmlFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlFile(
              '/home/sb/expected.xml', '<foo><baz/></foo>');
        }
    }

.. code-block:: bash

    $ phpunit XmlStringEqualsXmlFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlStringEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlString:

assertXmlStringEqualsXmlString()
################################

``assertXmlStringEqualsXmlString(string $expectedXml, string $actualXml[, string $message = ''])``

Сообщает об ошибке, определённой в ``$message``, если XML-документ в ``$actualXml`` не равен XML-документу в ``$expectedXml``.

``assertXmlStringNotEqualsXmlString()`` — это противоположный этому утверждению, принимающий те же самые аргументы.

.. code-block:: php
    :caption: Использование assertXmlStringEqualsXmlString()
    :name: appendixes.assertions.assertXmlStringEqualsXmlString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlStringEqualsXmlStringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlString(
              '<foo><bar/></foo>', '<foo><baz/></foo>');
        }
    }

.. code-block:: bash

    $ phpunit XmlStringEqualsXmlStringTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) XmlStringEqualsXmlStringTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlStringTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.


