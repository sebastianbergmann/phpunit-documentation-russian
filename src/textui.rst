

.. _textui:

============================
Исполнитель тестов командной строки
============================

Исполнитель тестов (test runner) командной строки PHPUnit можно запустить с помощью
команды :file:`phpunit`. Следующий пример показывает, как запускать
тесты с помощью этого инструмента командной строки PHPUnit:

.. code-block:: bash

    $ phpunit ArrayTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ..

    Time: 0 seconds

    OK (2 tests, 2 assertions)

При вводе команды, как показано выше, исполнитель тестов командной строки PHPUnit будет
искать исходный файл :file:`ArrayTest.php` в текущей рабочей директории,
загрузит его с целью найти в нём класс теста ``ArrayTest``. Затем он выполнит тесты этого класса.

Для каждого тестового запуска инструмент командной строки PHPUnit выведет один символ для
обозначения прогресса:

``.``

    Выводится, если тест успешно пройден.

``F``

    Выводится, когда утверждение не проходит во время выполнения тестового метода.

``E``

    Выводится, когда возникает ошибка во время запуска тестового метода.

``R``

    Выводится, когда тест был отмечен как рискованный (risky) (см.
    :ref:`risky-tests`).

``S``

    Выводится, когда тест был пропущен (см.
    :ref:`incomplete-and-skipped-tests`).

``I``

    Выводится, когда тест отмечен как незавершённый (incomplete) или ещё не
    реализован (см. :ref:`incomplete-and-skipped-tests`).

PHPUnit различает *неудачные выполнения (failures)* и
*ошибки (errors)*. Неудачное выполнение - это непройденное утверждение PHPUnit,
например вызов ``assertSame()``.
Ошибка - необработанное исключение или ошибка PHP. Иногда
это различие оказывается полезным, поскольку ошибки гораздо легче исправить, чем
неудачные выполнения. В случае большого списка проблем, лучше всего сначала
устранить ошибки и посмотреть, остались ли ещё какие-либо неудачные выполнения,
когда ошибки исправлены.

.. _textui.clioptions:

Опции командной строки
######################

Давайте посмотрим на опции командной строки исполнителя тестов в следующем коде:

.. code-block:: bash

    $ phpunit --help
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    Usage: phpunit [options] UnitTest [UnitTest.php]
           phpunit [options] <directory>

    Code Coverage Options:

      --coverage-clover <file>    Generate code coverage report in Clover XML format.
      --coverage-crap4j <file>    Generate code coverage report in Crap4J XML format.
      --coverage-html <dir>       Generate code coverage report in HTML format.
      --coverage-php <file>       Export PHP_CodeCoverage object to file.
      --coverage-text=<file>      Generate code coverage report in text format.
                                  Default: Standard output.
      --coverage-xml <dir>        Generate code coverage report in PHPUnit XML format.
      --whitelist <dir>           Whitelist <dir> for code coverage analysis.
      --disable-coverage-ignore   Disable annotations for ignoring code coverage.

    Logging Options:

      --log-junit <file>          Log test execution in JUnit XML format to file.
      --log-teamcity <file>       Log test execution in TeamCity format to file.
      --testdox-html <file>       Write agile documentation in HTML format to file.
      --testdox-text <file>       Write agile documentation in Text format to file.
      --testdox-xml <file>        Write agile documentation in XML format to file.
      --reverse-list              Print defects in reverse order

    Test Selection Options:

      --filter <pattern>          Filter which tests to run.
      --testsuite <name,...>      Filter which testsuite to run.
      --group ...                 Only runs tests from the specified group(s).
      --exclude-group ...         Exclude tests from the specified group(s).
      --list-groups               List available test groups.
      --list-suites               List available test suites.
      --test-suffix ...           Only search for test in files with specified
                                  suffix(es). Default: Test.php,.phpt

    Test Execution Options:

      --dont-report-useless-tests Do not report tests that do not test anything.
      --strict-coverage           Be strict about @covers annotation usage.
      --strict-global-state       Be strict about changes to global state
      --disallow-test-output      Be strict about output during tests.
      --disallow-resource-usage   Be strict about resource usage during small tests.
      --enforce-time-limit        Enforce time limit based on test size.
      --disallow-todo-tests       Disallow @todo-annotated tests.

      --process-isolation         Run each test in a separate PHP process.
      --globals-backup            Backup and restore $GLOBALS for each test.
      --static-backup             Backup and restore static attributes for each test.

      --colors=<flag>             Use colors in output ("never", "auto" or "always").
      --columns <n>               Number of columns to use for progress output.
      --columns max               Use maximum number of columns for progress output.
      --stderr                    Write to STDERR instead of STDOUT.
      --stop-on-error             Stop execution upon first error.
      --stop-on-failure           Stop execution upon first error or failure.
      --stop-on-warning           Stop execution upon first warning.
      --stop-on-risky             Stop execution upon first risky test.
      --stop-on-skipped           Stop execution upon first skipped test.
      --stop-on-incomplete        Stop execution upon first incomplete test.
      --fail-on-warning           Treat tests with warnings as failures.
      --fail-on-risky             Treat risky tests as failures.
      -v|--verbose                Output more verbose information.
      --debug                     Display debugging information.

      --loader <loader>           TestSuiteLoader implementation to use.
      --repeat <times>            Runs the test(s) repeatedly.
      --teamcity                  Report test execution progress in TeamCity format.
      --testdox                   Report test execution progress in TestDox format.
      --testdox-group             Only include tests from the specified group(s).
      --testdox-exclude-group     Exclude tests from the specified group(s).
      --printer <printer>         TestListener implementation to use.

    Configuration Options:

      --bootstrap <file>          A "bootstrap" PHP file that is run before the tests.
      -c|--configuration <file>   Read configuration from XML file.
      --no-configuration          Ignore default configuration file (phpunit.xml).
      --no-coverage               Ignore code coverage configuration.
      --no-extensions             Do not load PHPUnit extensions.
      --include-path <path(s)>    Prepend PHP's include_path with given path(s).
      -d key[=value]              Sets a php.ini value.
      --generate-configuration    Generate configuration file with suggested settings.

    Miscellaneous Options:

      -h|--help                   Prints this usage information.
      --version                   Prints the version and exits.
      --atleast-version <min>     Checks that version is greater than min and exits.

``phpunit UnitTest``

    Запускает тесты, представленные в классе ``UnitTest``. Ожидается, что этот класс будет объявлен
    в исходном файле :file:`UnitTest.php`.

    ``UnitTest`` должен быть либо классом, который наследуется от ``PHPUnit\Framework\TestCase``, либо классом,
    с методом ``public static suite()``, возвращающий объект типа ``PHPUnit\Framework\Test``, например,
    экземпляр класса ``PHPUnit\Framework\TestSuite``.

``phpunit UnitTest UnitTest.php``

    Выполняет тесты в классе ``UnitTest``. Ожидается, что этот класс будет
    объявлен в указанном исходном файле.

``--coverage-clover``

    Генерирует файл логов в формате XML с информацией о покрытии кода тестами для выполненных тестов.
    См. :ref:`logging` для получения более подробной информации.

    Обратите внимание, что данная функциональность доступна только в случае
    установленных расширений tokenizer и Xdebug.

``--coverage-crap4j``

    Генерирует отчёт о покрытии кода тестами в формате Crap4j.
    См. :ref:`code-coverage-analysis` для получения более подробной информации.

    Обратите внимание, что данная функциональность доступна только в случае
    установленных расширений tokenizer и Xdebug.

``--coverage-html``

    Генерирует отчёт о покрытии кода тестами в формате HTML.
    См. :ref:`code-coverage-analysis` для получения более подробной информации.

    Обратите внимание, что данная функциональность доступна только в случае
    установленных расширений tokenizer и Xdebug.

``--coverage-php``

    Генерирует сериализованный объект класса PHP_CodeCoverage с
    информацией о покрытии кода тестами.

    Обратите внимание, что данная функциональность доступна только в случае
    установленных расширений tokenizer и Xdebug.

``--coverage-text``

    Генерирует файл логов или вывод командной строки в человекочитаемом формате
    с информацией о покрытии кода тестами для запуска тестов.
    См. :ref:`logging` для получения более подробной информации.

    Обратите внимание, что данная функциональность доступна только в случае
    установленных расширений tokenizer и Xdebug.

``--log-junit``

    Генерирует файл журнала (logfile) в формате JUnit XML для запуска тестов.
    См. :ref:`logging` для получения более подробной информации.

``--testdox-html`` и ``--testdox-text``

    Генерирует agile-документацию в HTML или текстовом формате для запущенных тестов
    (см. :ref:`textui.testdox`).

``--filter``

    Выполняются только те тесты, названия которых совпадают с шаблоном регулярного выражения.
    Если шаблон не заключён в разделители, PHPUnit будет заключать шаблон в разделители ``/``.

    Имена тестов для совпадения может быть в одном из следующих форматов:

    ``TestNamespace\TestCaseClass::testMethod``

        Формат имени теста по умолчанию эквивалентен использованию магической константы
        ``__METHOD__`` внутри тестового метода.

    ``TestNamespace\TestCaseClass::testMethod with data set #0``

        Когда в тесте есть провайдер данных, каждая итерация данных
        получает текущий индекс, добавленный в концу имени теста по умолчанию.

    ``TestNamespace\TestCaseClass::testMethod with data set "my named data"``

        Когда в тесте есть провайдер данных, использующий именованные наборы, каждая
        итерация данных получает текущее название, добавленное к
        концу имени теста по умолчанию. См.
        :numref:`textui.examples.TestCaseClass.php` для просмотра примера
        именованных наборов данных.

        .. code-block:: php
            :caption: Именованные наборы данных
            :name: textui.examples.TestCaseClass.php

            <?php
            use PHPUnit\Framework\TestCase;

            namespace TestNamespace;

            class TestCaseClass extends TestCase
            {
                /**
                 * @dataProvider provider
                 */
                public function testMethod($data)
                {
                    $this->assertTrue($data);
                }

                public function provider()
                {
                    return [
                        'my named data' => [true],
                        'my data'       => [true]
                    ];
                }
            }
            ?>

    ``/path/to/my/test.phpt``

        Путь в файловой системе к имени теста типа PHPT.

    См. :numref:`textui.examples.filter-patterns` для примеров
    корректных шаблонов фильтров.

    .. code-block:: shell
        :caption: Примеры шаблонов фильтров
        :name: textui.examples.filter-patterns

        --filter 'TestNamespace\\TestCaseClass::testMethod'
        --filter 'TestNamespace\\TestCaseClass'
        --filter TestNamespace
        --filter TestCaseClase
        --filter testMethod
        --filter '/::testMethod .*"my named data"/'
        --filter '/::testMethod .*#5$/'
        --filter '/::testMethod .*#(5|6|7)$/'

    См. :numref:`textui.examples.filter-shortcuts` для некоторых
    дополнительных сокращений (shortcuts), доступных для сопоставления с
    провайдерами данных.

    .. code-block:: shell
        :caption: Сокращения фильтра
        :name: textui.examples.filter-shortcuts

        --filter 'testMethod#2'
        --filter 'testMethod#2-4'
        --filter '#2'
        --filter '#2-4'
        --filter 'testMethod@my named data'
        --filter 'testMethod@my.*data'
        --filter '@my named data'
        --filter '@my.*data'

``--testsuite``

    Выполняется только тот тестовый набор, который совпадает с заданным шаблоном.

``--group``

    Выполняются только тесты из указанных групп. Тест можно назначить
    группе, используя аннотацию ``@group`.

    Аннотация ``@author`` - это псевдоним для ``@group``, позволяющий фильтровать тесты по их авторам.

``--exclude-group``

    Исключить тесты из указанных групп. Тест можно назначить
    группе, используя аннотацию ``@group`.

``--list-groups``

    Список доступных групп тестов.

``--test-suffix``

    Только поиск тестовых файлов с указанными суффиксами.

``--dont-report-useless-tests``

    Не сообщать о тестах, которые ничего не тестируют. См. :ref:`risky-tests` для получения подробной информации.

``--strict-coverage``

    Строгая проверка непроизвольного охвата тестами кода. См. :ref:`risky-tests` для получения подробной информации.

``--strict-global-state``

    Строгая проверка относительно манипуляций с глобальным состоянием. See :ref:`risky-tests` для получения подробной информации.

``--disallow-test-output``

    Строгая проверка относительно вывода во время выполнения тестов. See :ref:`risky-tests` для получения подробной информации.

``--disallow-todo-tests``

    Не выполнять тесты с аннотацией ``@todo`` в её docblock.

``--enforce-time-limit``

    Применить ограничение по времени, основываясь на размер теста. См. :ref:`risky-tests` для получения более подробной информации.

``--process-isolation``

    Запускать каждый тест в отдельном процессе PHP.

``--no-globals-backup``

    Не создавать резервную копию и восстанавливать суперглобальный массив $GLOBALS. См. :ref:`fixtures.global-state`
    для получения более подробной информации..

``--static-backup``

    Резервное копирование и восстановление статических атрибутов пользовательских классов.
    См. :ref:`fixtures.global-state` для получения более подробной информации..

``--colors``

    Использовать цвета в выводе.
    В Windows используйте `ANSICON <https://github.com/adoxa/ansicon>`_ или `ConEmu <https://github.com/Maximus5/ConEmu>`_.

    Существует три возможных значения этой опции:

    -

      ``never``: никогда не отображать цвета в выводе. Это значение по умолчанию, когда не используется опция ``--colors``.

    -

      ``auto``: отображает цвета в выводе, за исключением, если текущий терминал не поддерживает цвета, либо
      если вывод не был передан в другую команду или не перенаправлен в файл.

    -

      ``always``: всегда отображать цвета в выводе, даже если текущий терминал не поддерживает цвета, или
      когда вывод передаётся в команду или перенаправляется в file.

    Когда ``--colors`` используется без значения, используется ``auto``.

``--columns``

    Определяет количество столбцов для вывода прогресса выполнения тестов.
    Если задано значение ``max``, количество столбцов будет максимальным для текущего терминала.

``--stderr``

    Необязательно печатать в ``STDERR`` вместо ``STDOUT``.

``--stop-on-error``

    Прекратить выполнение при первой ошибке.

``--stop-on-failure``

    Прекратить выполнение при первой ошибке или неудачном выполнении.

``--stop-on-risky``

    Прекратить выполнение при первом рискованном тесте.

``--stop-on-skipped``

    Прекратить выполнение при первом пропущенном тесте.

``--stop-on-incomplete``

    Прекратить выполнение при первом незавершённом тесте.

``--verbose``

    Выводить более подробную информацию, например, имена незавершённых или пропущенных тестов.

``--debug``

    Выводить отладочную информацию, такую как название теста при его запуске.

``--loader``

    Указывает используемую реализацию загрузчика ``PHPUnit\Runner\TestSuiteLoader``

    Стандартный загрузчик тестового набора будет искать исходный файл теста
    в текущей рабочей директории и в каждой директории, указанной в конфигурационной
    директиве PHP ``include_path``.
    Имя класса, такое как ``Project_Package_Class``, сопоставляется с исходным файлом
    :file:`Project/Package/Class.php`.

``--repeat``

    Повторять выполнение тестов указанное количество раз.

``--testdox``

    Сообщает о ходе тестирования в формате TestDox (см. :ref:`textui.testdox`).

``--printer``

    Указывает используемую реализацию форматирования вывода (printer). Этот класс должен наследоваться от
    ``PHPUnit\Util\Printer`` и реализовывать интерфейс
    ``PHPUnit\Framework\TestListener``.

``--bootstrap``

    "Загрузочный" ("bootstrap") файл PHP, который будет запускаться перед выполнением тестов.

``--configuration``, ``-c``

    Прочитать конфигурацию из XML-файла.
    См. :ref:`appendixes.configuration` для получения более подробной информации.

    Если файл :file:`phpunit.xml` или
    :file:`phpunit.xml.dist` (в таком порядке) существуют в
    текущей рабочей директории, а опция ``--configuration``
    *не* используется, конфигурация будет автоматически прочитана
    из этого файла.

    Если директория указана и файл
    :file:`phpunit.xml` или :file:`phpunit.xml.dist` (в таком порядке)
    существует в этой директории, конфигурация будет автоматически загружена
    из этого файла.

``--no-configuration``

    Игнорировать :file:`phpunit.xml` и
    :file:`phpunit.xml.dist` из текущей рабочей
    директории.

``--include-path``

    Добавить в опцию PHP ``include_path`` указанные пути.

``-d``

    Устанавливает значение заданной опции конфигурации PHP.

.. admonition:: Note

   Обратите внимание, что с версии 4.8 параметры могут быть указаны после аргументов.

.. _textui.testdox:

TestDox
#######

Функциональность TestDox PHPUnit просматривает тестовый класс и все названия его
тестовых методов, и преобразует их из имён PHP в стиле написания camel case (или snake_case) в предложения:
``testBalanceIsInitiallyZero()`` (или ``test_balance_is_initially_zero()``) становится "Balance is
initially zero". Если есть несколько тестовых методов, названия которых отличаются
только одной или более цифрой на конце, например
``testBalanceCannotBecomeNegative()`` и
``testBalanceCannotBecomeNegative2()``, предложение
"Balance cannot become negative" появится только один раз, при условии, что
все эти тесты успешно прошли.

Давайте посмотрим aglie-документацию, сгенерированную для класса ``BankAccount``:

.. code-block:: bash

    $ phpunit --testdox BankAccountTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    BankAccount
     ✔ Balance is initially zero
     ✔ Balance cannot become negative

В качестве альтернативы, aglie-документация может быть сгенерирована в HTML или
текстовом формате и записана в файл, используя аргументы ``--testdox-html``
и ``--testdox-text``.

Документация Agile может использоваться для документирования предположений, которые вы делаете относительно внешних пакетов, используемых проекте.
Когда вы используете внешний пакет, вы подвержены рискам, что пакет не будет работать так, как ожидалось, то есть он изменит своё поведение,
а будущие версии пакета изменятся завуалированным способом, тем самым ломая ваш код, даже не подозревая об этом.
Вы можете снизить эти риски, путём написания каждый раз теста, когда вы делаете предположение.
Если тест проходит, значит ваше предположение верно.
Если вы будете документировать все свои предположения с помощью тестов, новые версии внешнего пакета не будут вызывать беспокойства:
если тесты проходят, то система должна продолжать работать.

