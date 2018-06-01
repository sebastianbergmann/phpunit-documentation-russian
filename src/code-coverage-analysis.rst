

.. _code-coverage-analysis:

====================
Анализ покрытия кода
====================

    *Wikipedia*:

    В информатике покрытие кода - мера, используемая для описания степени,
    в которой исходной код программы протестирован определённым набором тестов.
    Программа с высоким покрытием кода была более тщательно протестирована и имеет меньше шансов содержать ошибки программного обеспечения, чем программа с низким покрытием кода тестами.

В этой главе вы узнаете всё о функциональности покрытия кода (code coverage) PHPUnit,
которая даёт представление о том, какие части кода выполняются при выполнении тестов.
Она использует компонент `php-code-coverage <https://github.com/sebastianbergmann/php-code-coverage>`_,
который, в свою очередь, использует функциональность покрытия кода, предоставляемую
PHP-расширением `Xdebug <https://xdebug.org/>`_.

.. admonition:: Note

   Xdebug не распространяется как часть PHPUnit. Если во время тестирования вы получаете
   уведомление о том, что драйвер покрытия кода отсутствует, это означает,
   что Xdebug либо не установлен, либо неправильно настроен.
   Прежде чем вы сможете использовать возможности анализа покрытия кода,
   вы должны `прочитать руководство по установке Xdebug <https://xdebug.org/docs/install>`_.

   php-code-coverage также поддерживает `phpdbg <https://phpdbg.room11.org/introduction.html>`_
   в качестве альтернативного источника для данных покрытия кода.

PHPUnit может генерировать отчёт о покрытии кода на основе HTML, а также лог-файлы в представлении XML
с информацией о покрытии кода в различных форматах (Clover, Crap4J, PHPUnit).
Информация о покрытии кода также может быть представлена в виде текста
(и напечатана в STDOUT) и экспортирована как код PHP для дальнейшей обработки.

Пожалуйста, обратитесь к :ref:`textui` для просмотра списка переключателей (switches) командной строки,
которые управляют функциональностью покрытия кода, а также :ref:`appendixes.configuration.logging`
для получения соответствующих параметров конфигурации.

.. _code-coverage-analysis.metrics:

Показатели программного обеспечения покрытия кода
#################################################

Существуют различные показатели (метрики) программного обеспечения (далее просто "показатель") для оценки покрытия кода:

*Покрытие строки (Line Coverage)*

    Показатель *Line Coverage* определяет,
    была ли выполнена каждая исполняемая строка.

*Покрытие функции и метода (Function and Method Coverage)*

    Показатель *Function and Method Coverage*
    определяет, была ли вызвана каждая функция или метод.
    php-code-coverage рассматривает функцию или метод как покрытую (covered),
    только когда все исполняемые строки покрыты.

*Покрытие класса и трейта (Class and Trait Coverage)*

    Показатель *Class and Trait Coverage*
    определяет, был ли покрыт каждый метод класса или трейта.
    php-code-coverage рассматривает класс или трейт как покрытый,
    только когда все их методы покрыты.

*Покрытие кода операции (Opcode Coverage)*

    Показатель *Opcode Coverage* определяет, выполнялся ли каждый опкод (код операции, opcode)
    функции или метода во время выполнения набора тестов. Строка кода обычно компилируется
    в несколько кодов опкодов. Line Coverage рассматривает строку как покрытую, как только
    один из опкодов будет выполнен.

*Покрытие ветки (Branch Coverage)*

    Показатель *Branch Coverage* определяет, было ли логическое выражение каждой
    управляющей структуры оценено как ``true``, так и ``false` при выполнении набора тестов.

*Покрытие пути (Path Coverage)*

    Показатель *Path Coverage* определяет, использовался ли каждый из возможных
    путей выполнения функции или метода во время выполнения набора тестов.
    Путь выполнения - это уникальная последовательность ветвей от входа функции или метода до его выхода.

*Индекс Change Risk Anti-Patterns (CRAP)*

    Индекс *Change Risk Anti-Patterns (CRAP)*
    рассчитывается на основе цикломатической сложности и покрытия кода
    единицы (unit) кода. Код, который не слишком сложный и имеет адекватный процент
    покрытия кода, будет иметь низкий индекс CRAP. Индекс CRAP может быть снижен
    путём написания тестов и рефакторинга тестов для уменьшения его сложности.

.. admonition:: Примечание

   Показатели *Opcode Coverage*,
   *Branch Coverage* и
   *Path Coverage* ещё не поддерживаются
   php-code-coverage.

.. _code-coverage-analysis.whitelisting-files:

Белый список файлов
###################

Необходимо настроить *белый список* (*whitelist*) для указания
PHPUnit, какие файлы исходного кода следует включить в отчёт о покрытии кода.
Это можно сделать либо используя опцию командной строки ``--whitelist``,
либо через файл конфигурации (см. :ref:`appendixes.configuration.whitelisting-files`).

Доступны параметры конфигурации ``addUncoveredFilesFromWhitelist`` и ``processUncoveredFilesFromWhitelist``
для настройки использования белого списка:

- ``addUncoveredFilesFromWhitelist="false"`` означает, что в отчёт о покрытии кода будут включены только файлы из белого списка, содержащие хотя бы одну строку выполненного кода
- ``addUncoveredFilesFromWhitelist="true"`` (по умолчанию) означает, что все файлы из белого списка будут включены в отчёт о покрытии кода, даже если ни одна строка кода такого файла не была выполнена
- ``processUncoveredFilesFromWhitelist="false"`` (по умолчанию) означает, что в отчёт о покрытии кода будет включены файлы из белого списка, у которых нет исполненных строк кода (если установлено ``addUncoveredFilesFromWhitelist="true"``), но он не будет загружен PHPUnit и поэтому не будет анализироваться для корректной информации о исполненных строк кода
- ``processUncoveredFilesFromWhitelist="true"`` означает, что файл в белом списке, у которого нет исполненных строк кода, будет загружен PHPUnit, чтобы его можно было анализировать для корректной информации о исполненных строк

.. admonition:: Note

   Обратите внимание, что загрузка файлов исходного кода, выполняемая при установке
   ``processUncoveredFilesFromWhitelist="true"``, может вызвать проблемы, например,
   когда файл исходного кода содержит код вне области класса или функции.

.. _code-coverage-analysis.ignoring-code-blocks:

Игнорирование блоков кода
#########################

Иногда у вас есть блоки кода, которые вы не можете протестировать и поэтому вы можете
игнорировать при анализе покрытия кода. PHPUnit позволяет сделать это с использованием
аннотаций ``@codeCoverageIgnore``,
``@codeCoverageIgnoreStart`` и
``@codeCoverageIgnoreEnd``, как показано в
:numref:`code-coverage-analysis.ignoring-code-blocks.examples.Sample.php`.

.. code-block:: php
    :caption: Использование ``@codeCoverageIgnore``, ``@codeCoverageIgnoreStart`` и ``@codeCoverageIgnoreEnd`` annotations
    :name: code-coverage-analysis.ignoring-code-blocks.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @codeCoverageIgnore
     */
    class Foo
    {
        public function bar()
        {
        }
    }

    class Bar
    {
        /**
         * @codeCoverageIgnore
         */
        public function foo()
        {
        }
    }

    if (false) {
        // @codeCoverageIgnoreStart
        print '*';
        // @codeCoverageIgnoreEnd
    }

    exit; // @codeCoverageIgnore

Пропущенные строки кода (отмеченные как игнорируемые с помощью аннотаций)
считаются выполненными (если они могут быть исполнены) и не будут подсвечиваться.

.. _code-coverage-analysis.specifying-covered-methods:

Определение покрытых методов
############################

Аннотация ``@covers`` (см.
:ref:`appendixes.annotations.covers.tables.annotations`) может
использоваться в тестовом коде для указания, какие методы тестовый метод
хочет протестировать. Если она указана, то в информации о покрытии кода будут будут
только эти указанные методы.
:numref:`code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php`
показывает это на примере.

.. code-block:: php
    :caption: Тесты, в которых указывается, какой метод они хотят покрыть
    :name: code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class BankAccountTest extends TestCase
    {
        protected $ba;

        protected function setUp()
        {
            $this->ba = new BankAccount;
        }

        /**
         * @covers BankAccount::getBalance
         */
        public function testBalanceIsInitiallyZero()
        {
            $this->assertSame(0, $this->ba->getBalance());
        }

        /**
         * @covers BankAccount::withdrawMoney
         */
        public function testBalanceCannotBecomeNegative()
        {
            try {
                $this->ba->withdrawMoney(1);
            }

            catch (BankAccountException $e) {
                $this->assertSame(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::depositMoney
         */
        public function testBalanceCannotBecomeNegative2()
        {
            try {
                $this->ba->depositMoney(-1);
            }

            catch (BankAccountException $e) {
                $this->assertSame(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::getBalance
         * @covers BankAccount::depositMoney
         * @covers BankAccount::withdrawMoney
         */
        public function testDepositWithdrawMoney()
        {
            $this->assertSame(0, $this->ba->getBalance());
            $this->ba->depositMoney(1);
            $this->assertSame(1, $this->ba->getBalance());
            $this->ba->withdrawMoney(1);
            $this->assertSame(0, $this->ba->getBalance());
        }
    }

Также можно указать, что тест не должен покрывать
*какой-либо* метод, используя аннотацию
``@coversNothing`` (см.
:ref:`appendixes.annotations.coversNothing`). Это может быть
полезно при написании интеграционных тестов, чтобы убедиться, что вы
только генерируете покрытие кода с помощью модульных тестов.

.. code-block:: php
    :caption: Тест, который указывает, что ни один метод должен быть покрыт
    :name: code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php

    <?php
    use PHPUnit\DbUnit\TestCase

    class GuestbookIntegrationTest extends TestCase
    {
        /**
         * @coversNothing
         */
        public function testAddEntry()
        {
            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $queryTable = $this->getConnection()->createQueryTable(
                'guestbook', 'SELECT * FROM guestbook'
            );

            $expectedTable = $this->createFlatXmlDataSet("expectedBook.xml")
                                  ->getTable("guestbook");

            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }

.. _code-coverage-analysis.edge-cases:

Крайние случаи
##############

В этом разделе показаны заслуживающие внимания крайние случаи (edge cases), которые приводят к
путанице информации о покрытии кода.

.. code-block:: php
    :name: code-coverage-analysis.edge-cases.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    // Потому этот код "находится на одной строке", а в не отдельном блоке инструкций,
    // в одной строке всегда будет один статус покрытия
    if (false) this_function_call_shows_up_as_covered();

    // Из-за того, как покрытие кода работает внутри, эти две строки - особенные.
    // Эта строка будет отображаться как не исполняемая
    if (false)
        // Эта строка будет отображаться как покрытая, потому что на самом деле
        // покрытие оператора if в приведённой выше строке показано здесь!
        will_also_show_up_as_covered();

    // Чтобы избежать этого, необходимо использовать фигурные скобки
    if (false) {
        this_call_will_never_show_up_as_covered();
    }


