

.. _test-doubles:

=================
Тестовые двойники
=================

Джерард Месарош (Gerard Meszaros) вводит концепцию тестовых двойников в
:ref:`Meszaros2007` следующим образом:

    *Gerard Meszaros*:

    Иногда просто сложно проверить тестируемую систему,
    поскольку она зависит от других компонентов, которые невозможно использовать в тестовой среде.
    Это может быть связано из-за недоступности компонентов или отсутствия необходимых тесту возвращаемых
    значений или из-за нежелательных побочных эффектов при их выполнении.
    В других случаях стратегия тестирования может потребовать большей видимости
    или контроля над внутренним поведением SUT.

    Когда мы пишем тест, в котором мы не можем (или не хотим) использовать настоящий (реальный)
    зависимый компонент (depended-on component, DOC),
    мы можем заменить его тестовым двойником. Тестовый двойник необязательно
    должен вести себя, как настоящий DOC. От него требуется предоставить такой же API, чтобы
    тестируемая система не отличала его от настоящего, считала, что он и есть реальный компонент!

Методы ``createMock($type)`` и ``getMockBuilder($type)``, предоставляемые PHPUnit могут
использоваться в тесте для автоматической генерации объекта, который может действовать как тестовый
двойник для указанного типа (интерфейса или имени класса). Этот объект
тестового двойника может использоваться в каждом контексте,
где ожидается или требуется объект исходного (оригинального) типа.

Метод ``createMock($type)`` немедленно возвращает объект тестового двойника
для указанного типа (интерфейса или класса). Создание
этого тестового двойника осуществляется с использованием настроек по умолчанию.
Методы  ``__construct()`` и ``__clone()`` не выполняются, а аргументы, переданные методу тестового двойника,
не будут клонироваться. Если эти значения по умолчанию не нужны, вы можете использовать метод
``getMockBuilder($type)`` для настройки генерации тестового двойника, используя текучий (fluent) интерфейс.

По умолчанию все методы исходного класса заменяются фиктивной (dummy)
реализацией, которая просто возвращает ``null`` (без вызова
исходного метода). Например, используя метод ``will($this->returnValue())``,
вы можете настроить эти фиктивные реализации для возврата значения при вызове.

.. admonition:: Ограничение: окончательные, закрытые и статические методы

   Обратите внимание, что методы, объявленные как ``final``, ``private``,
   ``protected`` и ``static`` не могут быть подменены (stubbed) или имитированы (mocked).
   Они игнорируются функциональностью тестовых двойников PHPUnit
   и сохраняют своё первоначальное поведение, за исключением методов, объявленных как ``static``,
   которые будут заменены вызовом, выбрасывающим исключение
   ``\PHPUnit\Framework\MockObject\BadMethodCallException``.


.. _test-doubles.stubs:

Заглушки
########

Практика замены объекта тестовым двойником, который (необязательно)
возвращает сконфигурированные возвращаемые значения, называется *подмена* (*stubbing*).
Вы можете использовать *заглушку* (*stub*) «для замены настоящего компонента, от
которого зависит тестируемая система, чтобы обеспечить тест контрольной точкой для
опосредованного ввода тестируемой системы. Это позволяет тесту переключить
тестируемую систему на ветвь кода, не выполняемую в обычной ситуации.»

:numref:`test-doubles.stubs.examples.StubTest.php` показывает, как
вызывать методы заглушки и устанавливать возвращаемые значения. Сначала мы используем
метод ``createMock()``, предоставляемый классом
``PHPUnit\Framework\TestCase`` для установки объекта-заглушки,
который будет похож на объект ``SomeClass``
(:numref:`test-doubles.stubs.examples.SomeClass.php`). Затем мы
используем `текучий интерфейс <http://martinfowler.com/bliki/FluentInterface.html>`_,
который предоставляет PHPUnit, чтобы указать поведение для заглушки. По сути,
это означает, что вам не нужно создавать несколько временных объектов и
связывать их вместе впоследствии. Вместо этого вы вызываете цепочку методов, как показано
в примере. Это приводит к более читабельному и «текучему» коду.

.. code-block:: php
    :caption: Класс, который будет подменён (для него будет сделана заглушка)
    :name: test-doubles.stubs.examples.SomeClass.php

    <?php
    class SomeClass
    {
        public function doSomething()
        {
            // Сделать что-нибудь.
        }
    }

.. code-block:: php
    :caption: Подмена вызова метода для возврата фиксированного значения
    :name: test-doubles.stubs.examples.StubTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testStub()
        {
            // Создать заглушку для класса SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Настроить заглушку.
            $stub->method('doSomething')
                 ->willReturn('foo');

            // Вызов $stub->doSomething() теперь вернёт 'foo'.
            $this->assertSame('foo', $stub->doSomething());
        }
    }

.. admonition:: Ограничение: Методы с названием «method»

   Пример, показанный выше, работает только тогда, когда в исходном классе нет метода с названием «method».

   Если исходный класс объявляет метод, названный «method»,
   тогда для проверки утверждения нужно использовать
   ``$stub->expects($this->any())->method('doSomething')->willReturn('foo');``.

«За кулисами» PHPUnit автоматически генерирует новый PHP-класс, который
реализует желаемое поведение при использовании метода ``createMock()``.

:numref:`test-doubles.stubs.examples.StubTest2.php` показывает
пример использования текучего интерфейса Mock Builder для настройки
создания тестового двойника. Конфигурация этого тестового двойника использует
те же самые настройки по умолчанию, которые используются при ``createMock()``.

.. code-block:: php
    :caption: Используя API Mock Builder можно настроить генерируемый класс тестового двойника
    :name: test-doubles.stubs.examples.StubTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testStub()
        {
            // Создать заглушку для класса SomeClass.
            $stub = $this->getMockBuilder(SomeClass::class)
                         ->disableOriginalConstructor()
                         ->disableOriginalClone()
                         ->disableArgumentCloning()
                         ->disallowMockingUnknownTypes()
                         ->getMock();

            // Настроить заглушку.
            $stub->method('doSomething')
                 ->willReturn('foo');

            // Вызов $stub->doSomething() теперь вернёт 'foo'.
            $this->assertSame('foo', $stub->doSomething());
        }
    }

В приведённых примерах мы до сих пор возвращали простые значения, используя
``willReturn($value)``. Это короткий синтаксис делает то же, что и
``will($this->returnValue($value))``. Мы можем использовать вариации
этого более длинного синтаксиса для достижения более сложного поведения заглушки.

Иногда вы хотите вернуть один из аргументов вызванного метода (без изменений)
в качестве результата вызова подмены метода.
:numref:`test-doubles.stubs.examples.StubTest3.php` показывает, как вы
можете сделать этого, используя ``returnArgument()`` вместо
``returnValue()``.

.. code-block:: php
    :caption: Подмена вызова метода для возврата одного из аргументов
    :name: test-doubles.stubs.examples.StubTest3.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnArgumentStub()
        {
            // Создать заглушку для класса SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Настроить заглушку.
            $stub->method('doSomething')
                 ->will($this->returnArgument(0));

            // $stub->doSomething('foo') вернёт 'foo'
            $this->assertSame('foo', $stub->doSomething('foo'));

            // $stub->doSomething('bar') вернёт 'bar'
            $this->assertSame('bar', $stub->doSomething('bar'));
        }
    }

При тестировании текучего интерфейса иногда полезно, чтобы подменённый
метод возвращал ссылку на самого себя (объект-заглушку).
:numref:`test-doubles.stubs.examples.StubTest4.php` показывает, как вы
можете использовать ``returnSelf()`` для достижения этого.

.. code-block:: php
    :caption:Подмена вызова метода для возврата ссылки на объект заглушки
    :name: test-doubles.stubs.examples.StubTest4.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnSelf()
        {
            // Создать заглушку для класса SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Настроить заглушку.
            $stub->method('doSomething')
                 ->will($this->returnSelf());

            // $stub->doSomething() вернёт $stub
            $this->assertSame($stub, $stub->doSomething());
        }
    }

Иногда подменённый метод должен возвращать разные значения в зависимости от
предопределённого списка аргументов.  Вы можете использовать
``returnValueMap()`` для создания сопоставления, которое привязывает
аргументы к соответствующим возвращаемым значениям. См.
:numref:`test-doubles.stubs.examples.StubTest5.php`.

.. code-block:: php
    :caption: Подмена вызова метода для возврата значения из карты
    :name: test-doubles.stubs.examples.StubTest5.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnValueMapStub()
        {
            // Создать заглушку для класса SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Создать карту аргументов для возврата значений
            $map = [
                ['a', 'b', 'c', 'd'],
                ['e', 'f', 'g', 'h']
            ];

            // Настроить заглушку.
            $stub->method('doSomething')
                 ->will($this->returnValueMap($map));

            // $stub->doSomething() возвращает разные значения в зависимости
            // от предоставленного списка.
            $this->assertSame('d', $stub->doSomething('a', 'b', 'c'));
            $this->assertSame('h', $stub->doSomething('e', 'f', 'g'));
        }
    }

Когда вызов подменённого метода должен вернуть вычисленное значение вместо
фиксированного (см. ``returnValue()``) или (неизменённый)
аргумент (см. ``returnArgument()``), вы можете использовать
``returnCallback()``, чтобы подменённый метод возвращал
результат функции обратного вызова или метода. См.
:numref:`test-doubles.stubs.examples.StubTest6.php`.

.. code-block:: php
    :caption: Подмена вызова метода для возврата значения из функции обратного вызова
    :name: test-doubles.stubs.examples.StubTest6.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnCallbackStub()
        {
            // Создать заглушку для класса SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Настроить заглушку.
            $stub->method('doSomething')
                 ->will($this->returnCallback('str_rot13'));

            // Вызов $stub->doSomething($argument) вернёт str_rot13($argument)
            $this->assertSame('fbzrguvat', $stub->doSomething('something'));
        }
    }

Более простой альтернативой настройке метода обратного вызова может быть
указание списка ожидаемых возвращаемых значений. Вы можете сделать это
с помощью метода ``onConsecutiveCalls()``. См.
:numref:`test-doubles.stubs.examples.StubTest7.php`.

.. code-block:: php
    :caption: Подмена вызова метода для возврата списка значений в указанном порядке
    :name: test-doubles.stubs.examples.StubTest7.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testOnConsecutiveCallsStub()
        {
            // Создать заглушку для класса SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Настроить заглушку.
            $stub->method('doSomething')
                 ->will($this->onConsecutiveCalls(2, 3, 5, 7));

            // Вызов $stub->doSomething() вернёт разное значение каждый раз
            $this->assertSame(2, $stub->doSomething());
            $this->assertSame(3, $stub->doSomething());
            $this->assertSame(5, $stub->doSomething());
        }
    }

Вместо возврата значения, подменённый метод может вызывать
исключение. :numref:`test-doubles.stubs.examples.StubTest8.php`
показывает как использовать ``throwException()`` для этого.

.. code-block:: php
    :caption: Подмена вызова метода для выбрасывания исключения
    :name: test-doubles.stubs.examples.StubTest8.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testThrowExceptionStub()
        {
            // Создать заглушку для класса SomeClass.
            $stub = $this->createMock(SomeClass::class);

            // Настроить заглушку.
            $stub->method('doSomething')
                 ->will($this->throwException(new Exception));

            // Вызов $stub->doSomething() выбрасывает исключение класса Exception
            $stub->doSomething();
        }
    }

Кроме того, вы можете написать заглушку самостоятельно и улучшить архитектуры
в процессе. Доступ к широко используемым ресурсам осуществляется через один фасад, поэтому вы
можете легко заменить ресурс заглушкой. Например, вместо непосредственных вызовов к базе
данных, разбросанных по всему коду, у вас может быть единственный объект ``Database`,
реализующий интерфейс ``IDatabase``. Затем вы можете создать заглушку для реализации
``IDatabase`` и использовать её в своих тестах. Вы даже можете создать опцию для запуска
тестов с этой заглушкой базы данных или реальной базы данных, чтобы вы могли использовать
ваши тесты как во время разработки, так и при тестировании интеграции с реальной базой данных.

Функциональность, которая должна быть подменена, имеет тенденцию группироваться в один и тот же объект.
Представляя функциональность одним, когерентным интерфейсом, вы уменьшаете связанность (coupling) с остальной частью
системы.

.. _test-doubles.mock-objects:

Подставные объекты
##################

Практика замены объекта тестовым двойником, который проверяет ожидания,
например, утверждая, что метод был вызван, называется *подстановкой* или *имитацией* (*mocking*).

Вы можете использовать *подставной объект* «в качестве точки наблюдения
для проверки опосредованного вывода тестируемой системы во время её работы. Обычно
подставной объект также содержит функциональность тестовой заглушки,
так как он должен возвращать значения в ответ на вызовы, но основное внимание
при его реализации уделяется проверке опосредованного вывода. Таким образом,
подставной объект — это значительно больше, чем просто тестовая заглушка
с дополнительными утверждениями: он используется совершенно иначе.» (Джерард Месарош).

.. admonition:: Ограничение: Автоматическая проверка ожиданий

   Только подставные объекты, сгенерированные в рамках теста, будут автоматически
   проверяться PHPUnit. Например, подставные объекты, созданные в провайдерах данных
   или введённые в тест с использованием аннотации ``@depends``,
   не проверяются автоматически PHPUnit.

Вот пример: предположим, что мы хотим проверить, что корректный метод
``update()`` в нашем примере вызывается на объекте, который
наблюдает за другим объектом. :numref:`test-doubles.mock-objects.examples.SUT.php`
показывает код для классов ``Subject`` и ``Observer``,
которые являются частью тестируемой системы.

.. code-block:: php
    :caption: Классы   Subject и Observer, которые являются частью тестируемой системы
    :name: test-doubles.mock-objects.examples.SUT.php

    <?php
    use PHPUnit\Framework\TestCase;

    class Subject
    {
        protected $observers = [];
        protected $name;

        public function __construct($name)
        {
            $this->name = $name;
        }

        public function getName()
        {
            return $this->name;
        }

        public function attach(Observer $observer)
        {
            $this->observers[] = $observer;
        }

        public function doSomething()
        {
            // Сделать что-нибудь.
            // ...

            // Уведомить наблюдателей, что мы что-то сделали.
            $this->notify('something');
        }

        public function doSomethingBad()
        {
            foreach ($this->observers as $observer) {
                $observer->reportError(42, 'Произошло что-то плохое', $this);
            }
        }

        protected function notify($argument)
        {
            foreach ($this->observers as $observer) {
                $observer->update($argument);
            }
        }

        // Другие методы.
    }

    class Observer
    {
        public function update($argument)
        {
            // Сделать что-нибудь.
        }

        public function reportError($errorCode, $errorMessage, Subject $subject)
        {
            // Сделать что-нибудь
        }

        // Другие методы.
    }

:numref:`test-doubles.mock-objects.examples.SubjectTest.php`
показывает, как использовать подставной объект для тестирования взаимодействия
между объектами ``Subject`` и ``Observer``.

Сначала мы используем метод ``getMockBuilder()``, предоставляемый
классом ``PHPUnit\Framework\TestCase`` для установки подставного объекта
для ``Observer``. Поскольку мы передаём массив в качестве
второго (необязательного) параметра для метода ``getMock()``,
только метод ``update()`` класса ``Observer`` заменяется реализацией подставного объекта.

Поскольку мы заинтересованы в проверке того, что метод вызывается и с какими
аргументы он вызывался, мы вводим методы ``expects()`` и
``with()``, чтобы указать, как должно выглядеть это взаимодействие.

.. code-block:: php
    :caption: Тестирование того, что метод вызывается один раз и с указанным аргументом
    :name: test-doubles.mock-objects.examples.SubjectTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testObserversAreUpdated()
        {
            // Создать подставной объект для Observer,
            // имитируя только метод update().
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['update'])
                             ->getMock();

            // Настроить ожидание для метода update(),
            // который должен вызваться только один раз со строкой 'something'
            // в качестве своего параметра.
            $observer->expects($this->once())
                     ->method('update')
                     ->with($this->equalTo('something'));

            // Создать объект Subject и присоединить
            // подставной объект Observer к нему.
            $subject = new Subject('My subject');
            $subject->attach($observer);

            // Вызвать метод doSomething() на объекте $subject,
            // который, как мы ожидаем, вызовет метод update()
            // подставного объекта Observer со строкой 'something'.
            $subject->doSomething();
        }
    }

Метод ``with()`` может принимать любое количество
аргументов, соответствующее количеству аргументов подставного
объекта. Вы можете указать более сложные ограничения аргументов
метода, чем простое сравнение.

.. code-block:: php
    :caption: Тестирование того, что метод вызывается с несколькими аргументами со своими ограничениями
    :name: test-doubles.mock-objects.examples.SubjectTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testErrorReported()
        {
            // Создать подставной объект для класса Observer, имитируя
            // метод reportError()
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['reportError'])
                             ->getMock();

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with(
                           $this->greaterThan(0),
                           $this->stringContains('Something'),
                           $this->anything()
                       );

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // Метод doSomethingBad() должен сообщить об ошибке наблюдателю
            // через метод reportError()
            $subject->doSomethingBad();
        }
    }

Метод ``withConsecutive()`` может принимать любое количество
массивов аргументов, в зависимости от вызовов, которые вы хотите протестировать.
Каждый массив — это список ограничений, соответствующих аргументам подставного
метода, как в ``with()``.

.. code-block:: php
    :caption: Тестирование того, что метод вызывается два раза с определёнными аргументами.
    :name: test-doubles.mock-objects.examples.with-consecutive.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testFunctionCalledTwoTimesWithSpecificArguments()
        {
            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['set'])
                         ->getMock();

            $mock->expects($this->exactly(2))
                 ->method('set')
                 ->withConsecutive(
                     [$this->equalTo('foo'), $this->greaterThan(0)],
                     [$this->equalTo('bar'), $this->greaterThan(0)]
                 );

            $mock->set('foo', 21);
            $mock->set('bar', 48);
        }
    }

Ограничение ``callback()`` может использоваться для более сложной
проверки аргументов. Это ограничение принимает функцию обратного вызова PHP
в качестве единственного аргумента. Функция обратного вызова PHP получит аргумент, который будет
проверяться как единственный аргумент, и должен возвращать ``true``, если
аргумент проходит проверку или ``false`` в противном случае.

.. code-block:: php
    :caption: Более сложная проверка аргументов
    :name: test-doubles.mock-objects.examples.SubjectTest3.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testErrorReported()
        {
            // Создать подставной объект длч Observer, имитируя
            // метод reportError()
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['reportError'])
                             ->getMock();

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with($this->greaterThan(0),
                            $this->stringContains('Something'),
                            $this->callback(function($subject){
                              return is_callable([$subject, 'getName']) &&
                                     $subject->getName() === 'My subject';
                            }));

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // Метод doSomethingBad() должен сообщить об ошибке наблюдателю
            // через метод reportError()
            $subject->doSomethingBad();
        }
    }

.. code-block:: php
    :caption: Проверка того, что метод вызывается один раз с идентичным переданным объектом
    :name: test-doubles.mock-objects.examples.clone-object-parameters-usecase.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed()
        {
            $expectedObject = new stdClass;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['foo'])
                         ->getMock();

            $mock->expects($this->once())
                 ->method('foo')
                 ->with($this->identicalTo($expectedObject));

            $mock->foo($expectedObject);
        }
    }

.. code-block:: php
    :caption: Создание подставного объекта с включённым клонированием параметров
    :name: test-doubles.mock-objects.examples.enable-clone-object-parameters.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed()
        {
            $cloneArguments = true;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->enableArgumentCloning()
                         ->getMock();

            // теперь подставной объект клонирует параметры, поэтому ограничение на идентичность (identicalTo)
            // терпит неудачу.
        }
    }

Таблица :ref:`appendixes.assertions.assertThat.tables.constraints`
показывает ограничения, которые могут быть применены к аргументам метода,
а в :numref:`test-doubles.mock-objects.tables.matchers`
показаны сопоставления, доступные для указания количества вызовов.

.. rst-class:: table
.. list-table:: Сопоставления (Matchers)
    :name: test-doubles.mock-objects.tables.matchers
    :header-rows: 1

    * - Сопоставление
      - Описание
    * - ``PHPUnit\Framework\MockObject\Matcher\AnyInvokedCount any()``
      - Возвращает соответствующее сопоставление, когда метод, для которого он вычисляется, выполняется ноль или более раз.
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount never()``
      - Возвращает соответствующее сопоставление, когда метод, для которого он вычисляется, никогда не выполняется.
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedAtLeastOnce atLeastOnce()``
      - Возвращает соответствующее сопоставление, когда метод, для которого он вычисляется, выполняется хотя бы один раз.
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount once()``
      - Возвращает соответствующее сопоставление, когда метод, для которого он вычисляется, выполняется ровно один раз.
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount exactly(int $count)``
      - Возвращает соответствующее сопоставление, когда метод, для которого он вычисляется, выполняется указанное в ``$count`` раз.
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedAtIndex at(int $index)``
      - Возвращает соответствующее сопоставление, когда метод, для которого он вычисляется, выполняется при заданном ``$index``.

.. admonition:: Примечание

   Параметр ``$index`` для сопоставления ``at()``
   относится к индексу, начинающемуся с нуля, во *всех вызовах метода*
   для заданного подставного объекта. Соблюдайте осторожность при
   использовании этого сопоставления, поскольку это может привести к хрупким (brittle) тестам,
   которые слишком тесно связаны с конкретными деталями реализации.

Как уже упоминалось в начале, когда значения по умолчанию, используемые методом
``createMock()`` при генерации тестового двойника, не соответствуют
ваши потребностям, то вы можете использовать метод ``getMockBuilder($type)``
для настройки генерации тестового двойника с использованием текучего интерфейса.
Вот список методов, предоставляемых Mock Builder:

-

  ``setMethods(array $methods)`` может вызываться в объекте Mock Builder для указания методов, которые должны быть заменены настраиваемым тестовым двойником. Поведение других методов не изменится. Если вы вызываете ``setMethods(null)``, то никакие методы не будут заменены.

-

  ``setMethodsExcept(array $methods)`` может вызываться в объекте Mock Builder для указания методов, которые не будут заменены настраиваемым тестовым двойником при замене всех остальных общедоступных методов. Это работает обратным образом для ``setMethods()``.

-

  ``setConstructorArgs(array $args)`` может вызываться для предоставления массива параметров, которые передаются конструктору исходного класса (который по умолчанию не заменяется фиктивной реализацией).

-

  ``setMockClassName($name)`` может использоваться для указания имени класса для генерируемого класса тестового двойника.

-

  ``disableOriginalConstructor()`` может использоваться для отключения вызова конструктора исходного класса.

-

  ``disableOriginalClone()`` может использоваться для отключения вызова конструктора исходного класса при клонировании.

-

  ``disableAutoload()`` может использоваться для отключения ``__autoload()`` во время генерации класса тестового двойника.

.. _test-doubles.prophecy:

Prophecy
########

`Prophecy <https://github.com/phpspec/prophecy>`_ -
«очень самоуверенный, но мощный и гибкий фрейморк для имитации PHP-объектов.
Хотя первоначально он был создан для удовлетворения потребностей phpspec2, он
достаточно гибкий, чтобы его можно было использовать внутри любого фреймворка тестирования
с минимальными усилиями».

PHPUnit имеет встроенную поддержку использования Prophecy для создания тестовых двойников.
:numref:`test-doubles.prophecy.examples.SubjectTest.php`
показывает, как один и тот же тест в :numref:`test-doubles.mock-objects.examples.SubjectTest.php`,
может быть переписан с использованием философии пророчеств (prophecies) и откровений (revelations) фреймворка Prophecy:

.. code-block:: php
    :caption: Тестирование того, что метод вызывается один раз с определённым аргументом
    :name: test-doubles.prophecy.examples.SubjectTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testObserversAreUpdated()
        {
            $subject = new Subject('My subject');

            // Создать prophecy для класса Observer.
            $observer = $this->prophesize(Observer::class);

            // Настроить ожидание для метода update(),
            // который должен вызваться только один раз со строкой 'something'
            // в качестве своего параметра.
            $observer->update('something')->shouldBeCalled();

            // Раскрыть (reveal) prophecy и привязать подставной объект
            // к Subject.
            $subject->attach($observer->reveal());

            // Вызвать метод doSomething() на объекте $subject,
            // который, как мы ожидаем, вызовет метод update()
            // подставного объекта Observer со строкой 'something'.
            $subject->doSomething();
        }
    }

Обратитесь к `документации <https://github.com/phpspec/prophecy#how-to-use-it>`_
по Prophecy для получения дополнительной информации о том, как создавать, настраивать и использовать
заглушки, шпионы и подстановки, используя этот альтернативный фреймворк тестовых двойников.

.. _test-doubles.mocking-traits-and-abstract-classes:

Имитация трейтов и абстрактных классов
######################################

Метод ``getMockForTrait()`` возвращает подставной объект,
который использует указанный трейт. Все абстрактные методы данного трейта
будут имитированы. Это позволяет проверить конкретные методы трейта.

.. code-block:: php
    :caption: Тестирование конкретных методов трейта
    :name: test-doubles.mock-objects.examples.TraitClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    trait AbstractTrait
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    class TraitClassTest extends TestCase
    {
        public function testConcreteMethod()
        {
            $mock = $this->getMockForTrait(AbstractTrait::class);

            $mock->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($mock->concreteMethod());
        }
    }

Метод ``getMockForAbstractClass()`` возвращает подставной объект
для абстрактного класса. Все абстрактные методы заданного абстрактного
класса имитируются. Это позволяет проверить конкретные методы абстрактного класса.

.. code-block:: php
    :caption: Тестирование конкретных методов абстрактного класса
    :name: test-doubles.mock-objects.examples.AbstractClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    abstract class AbstractClass
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    class AbstractClassTest extends TestCase
    {
        public function testConcreteMethod()
        {
            $stub = $this->getMockForAbstractClass(AbstractClass::class);

            $stub->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($stub->concreteMethod());
        }
    }

.. _test-doubles.stubbing-and-mocking-web-services:

Создание заглушек и имитация веб-сервисов
#########################################

Когда ваше приложение взаимодействует с веб-сервисами, вы хотите протестировать
его без фактического взаимодействия с веб-сервисом. Для упрощения создания заглушек
и имитации веб-сервисов, может использоваться метод ``getMockFromWsdl()``,
по аналогии с ``getMock()`` (см. выше). Единственное
отличие заключается в том, что ``getMockFromWsdl()`` возвращает заглушку или
подставной объект на основе описания веб-сервиса в формате WSDL, тогда как ``getMock()``
возвращает заглушку или подставной объект, основанный на классе или интерфейсе PHP.

:numref:`test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php`
показывает, как ``getMockFromWsdl()`` может использоваться для заглушки, например,
веб-сервиса, описанного в :file:`GoogleSearch.wsdl`.

.. code-block:: php
    :caption: Создание заглушки для веб-сервиса
    :name: test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class GoogleTest extends TestCase
    {
        public function testSearch()
        {
            $googleSearch = $this->getMockFromWsdl(
              'GoogleSearch.wsdl', 'GoogleSearch'
            );

            $directoryCategory = new stdClass;
            $directoryCategory->fullViewableName = '';
            $directoryCategory->specialEncoding = '';

            $element = new stdClass;
            $element->summary = '';
            $element->URL = 'https://phpunit.de/';
            $element->snippet = '...';
            $element->title = '<b>PHPUnit</b>';
            $element->cachedSize = '11k';
            $element->relatedInformationPresent = true;
            $element->hostName = 'phpunit.de';
            $element->directoryCategory = $directoryCategory;
            $element->directoryTitle = '';

            $result = new stdClass;
            $result->documentFiltering = false;
            $result->searchComments = '';
            $result->estimatedTotalResultsCount = 3.9000;
            $result->estimateIsExact = false;
            $result->resultElements = [$element];
            $result->searchQuery = 'PHPUnit';
            $result->startIndex = 1;
            $result->endIndex = 1;
            $result->searchTips = '';
            $result->directoryCategories = [];
            $result->searchTime = 0.248822;

            $googleSearch->expects($this->any())
                         ->method('doGoogleSearch')
                         ->will($this->returnValue($result));

            /**
             * $googleSearch->doGoogleSearch() теперь возвратит результат заглушки (stubbed result),
             * а метод doGoogleSearch() веб-сервиса не будет вызван.
             */
            $this->assertEquals(
              $result,
              $googleSearch->doGoogleSearch(
                '00000000000000000000000000000000',
                'PHPUnit',
                0,
                1,
                false,
                '',
                false,
                '',
                '',
                ''
              )
            );
        }
    }

.. _test-doubles.mocking-the-filesystem:

Имитация файловой системы
#########################

`vfsStream <https://github.com/mikey179/vfsStream>`_
— `обёртка потока <http://www.php.net/streams>`_ для
`виртуальной файловой системы <https://ru.wikipedia.org/wiki/%D0%92%D0%B8%D1%80%D1%82%D1%83%D0%B0%D0%BB%D1%8C%D0%BD%D0%B0%D1%8F_%D1%84%D0%B0%D0%B9%D0%BB%D0%BE%D0%B2%D0%B0%D1%8F_%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D0%B0>`_, которая может быть полезной в модульных тестах для имитации реальной файловой системы.

Просто добавьте зависимость ``mikey179/vfsStream`` в файл
``composer.json`` вашего проекта, если вы используете
`Composer <https://getcomposer.org/>`_ для управления
зависимостями в своём проекте. Вот самый минимальный файл
``composer.json``, который просто определяет зависимости
для разработки PHPUnit 4.6 и vfsStream:

.. code-block:: php

    {
        "require-dev": {
            "phpunit/phpunit": "~4.6",
            "mikey179/vfsStream": "~1"
        }
    }

:numref:`test-doubles.mocking-the-filesystem.examples.Example.php`
показывает класс, взаимодействующий с файловой системой.

.. code-block:: php
    :caption: Класс, взаимодействующий с файловой системой
    :name: test-doubles.mocking-the-filesystem.examples.Example.php

    <?php
    use PHPUnit\Framework\TestCase;

    class Example
    {
        protected $id;
        protected $directory;

        public function __construct($id)
        {
            $this->id = $id;
        }

        public function setDirectory($directory)
        {
            $this->directory = $directory . DIRECTORY_SEPARATOR . $this->id;

            if (!file_exists($this->directory)) {
                mkdir($this->directory, 0700, true);
            }
        }
    }

Без виртуальной файловой системы, такой как vfsStream, мы не можем протестировать
метод ``setDirectory()`` в изоляции от внешнего воздействия
(см. :numref:`test-doubles.mocking-the-filesystem.examples.ExampleTest.php`).

.. code-block:: php
    :caption: Тестирование класса, взаимодействующего с файловой системой
    :name: test-doubles.mocking-the-filesystem.examples.ExampleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExampleTest extends TestCase
    {
        protected function setUp()
        {
            if (file_exists(dirname(__FILE__) . '/id')) {
                rmdir(dirname(__FILE__) . '/id');
            }
        }

        public function testDirectoryIsCreated()
        {
            $example = new Example('id');
            $this->assertFalse(file_exists(dirname(__FILE__) . '/id'));

            $example->setDirectory(dirname(__FILE__));
            $this->assertTrue(file_exists(dirname(__FILE__) . '/id'));
        }

        protected function tearDown()
        {
            if (file_exists(dirname(__FILE__) . '/id')) {
                rmdir(dirname(__FILE__) . '/id');
            }
        }
    }

Приведённый выше подход имеет несколько недостатков:

-

  Как и в случае с любым внешним ресурсом, могут возникать периодические проблемы с файловой системой. Это делает взаимодействие с тестами непредсказуемым.

-

  В методах ``setUp()`` и ``tearDown()`` мы должны убедиться, что каталог не существует до и после теста.

-

  Когда выполнение теста завершается до того, как метод ``tearDown()`` будет выполнен, каталог останется в файловой системе.

:numref:`test-doubles.mocking-the-filesystem.examples.ExampleTest2.php`
показывает, как vfsStream может использоваться для имитации файловой системы в тесте
для класса, который взаимодействует с файловой системой.

.. code-block:: php
    :caption: Имитация файловой системы в тесте для класса, взаимодействующего с файловой системой
    :name: test-doubles.mocking-the-filesystem.examples.ExampleTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExampleTest extends TestCase
    {
        public function setUp()
        {
            vfsStreamWrapper::register();
            vfsStreamWrapper::setRoot(new vfsStreamDirectory('exampleDir'));
        }

        public function testDirectoryIsCreated()
        {
            $example = new Example('id');
            $this->assertFalse(vfsStreamWrapper::getRoot()->hasChild('id'));

            $example->setDirectory(vfsStream::url('exampleDir'));
            $this->assertTrue(vfsStreamWrapper::getRoot()->hasChild('id'));
        }
    }

Это имеет ряд преимуществ:

-

  Тест сам стал более кратким.

-

  vfsStream даёт разработчику теста полный контроль над тем, как выглядит окружение файловой системы для тестируемого кода.

-

  Поскольку операции файловой системы больше не выполняются на реальной файловой системе, операции очистки в методе ``tearDown()`` больше не требуются.


