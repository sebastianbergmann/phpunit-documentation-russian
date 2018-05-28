

.. _extending-phpunit:

==================
Расширение PHPUnit
==================

PHPUnit можно расширить различными способами для облегчения процесса написания тестов
и настройки обратной связи, получаемой от выполнения тестов. Вот общие отправные точки
для расширения PHPUnit.

.. _extending-phpunit.PHPUnit_Framework_TestCase:

Подкласс PHPUnit\\Framework\\TestCase
#####################################

Написать пользовательские утверждения и утилитные (вспомогательные) методы
в абстрактных подклассов ``PHPUnit\Framework\TestCase`` и наследуйте
ваши классы тестов из этого класса.
Это один из самых простых способов расширения PHPUnit.

.. _extending-phpunit.custom-assertions:

Написать пользовательские утверждения
#####################################

При написании пользовательских утверждений  лучше всего следовать
принципам реализации собственных утверждений PHPUnit.
Как вы можете видеть в
:numref:`extending-phpunit.examples.Assert.php`, метод
``assertTrue()`` - это просто обёртка над методами
``isTrue()`` и ``assertThat()``:
``isTrue()`` создаёт объект сопоставления (matcher object), который передаётся
``assertThat()`` для проведения оценки.

.. code-block:: php
    :caption: Методы assertTrue() и isTrue() класса PHPUnit\Framework\Assert
    :name: extending-phpunit.examples.Assert.php

    <?php
    namespace PHPUnit\Framework;

    use PHPUnit\Framework\TestCase;

    abstract class Assert
    {
        // ...

        /**
         * Утверждает, то условие истинно
         *
         * @param  boolean $condition
         * @param  string  $message
         * @throws PHPUnit\Framework\AssertionFailedError
         */
        public static function assertTrue($condition, $message = '')
        {
            self::assertThat($condition, self::isTrue(), $message);
        }

        // ...

        /**
         * Возвращает объект сопоставления PHPUnit\Framework\Constraint\IsTrue.
         *
         * @return PHPUnit\Framework\Constraint\IsTrue
         * @since  Method available since Release 3.3.0
         */
        public static function isTrue()
        {
            return new PHPUnit\Framework\Constraint\IsTrue;
        }

        // ...
    }?>

:numref:`extending-phpunit.examples.IsTrue.php` показывает, как
``PHPUnit\Framework\Constraint\IsTrue`` наследует
абстрактный базовый класс для объектов сопоставления (или ограничений),
``PHPUnit\Framework\Constraint``.

.. code-block:: php
    :caption: Класс PHPUnit\Framework\Constraint\IsTrue
    :name: extending-phpunit.examples.IsTrue.php

    <?php
    namespace PHPUnit\Framework\Constraint;

    use PHPUnit\Framework\Constraint;

    class IsTrue extends Constraint
    {
        /**
         * Evaluates the constraint for parameter $other. Returns true if the
         * constraint is met, false otherwise.
         *
         * @param mixed $other Value or object to evaluate.
         * @return bool
         */
        public function matches($other)
        {
            return $other === true;
        }

        /**
         * Returns a string representation of the constraint.
         *
         * @return string
         */
        public function toString()
        {
            return 'is true';
        }
    }?>

Усилия по реализации методов ``assertTrue()`` и
``isTrue()``, а также класса
``PHPUnit\Framework\Constraint\IsTrue`` дают
преимущество, состоящее в том, что ``assertThat()`` автоматически выполняет
оценку утверждения и задач отчётности, таких как подсчёт
статистики. Кроме того, метод ``isTrue()`` может использоваться
как сопоставление при настройке подставных объектов.

.. _extending-phpunit.PHPUnit_Framework_TestListener:

Реализация PHPUnit\\Framework\\TestListener
###########################################

:numref:`extending-phpunit.examples.SimpleTestListener.php`
показывает простую реализацию интерфейса ``PHPUnit\Framework\TestListener``.

.. code-block:: php
    :caption: Простой слушатель тестов
    :name: extending-phpunit.examples.SimpleTestListener.php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\Framework\TestListener;

    class SimpleTestListener implements TestListener
    {
        public function addError(PHPUnit\Framework\Test $test, Exception $e, $time)
        {
            printf("Ошибка во время выполнения теста '%s'.\n", $test->getName());
        }

        public function addFailure(PHPUnit\Framework\Test $test, PHPUnit\Framework\AssertionFailedError $e, $time)
        {
            printf("Тест '%s' провалился.\n", $test->getName());
        }

        public function addIncompleteTest(PHPUnit\Framework\Test $test, Exception $e, $time)
        {
            printf("Тест '%s' является неполным.\n", $test->getName());
        }

        public function addRiskyTest(PHPUnit\Framework\Test $test, Exception $e, $time)
        {
            printf("Тест '%s' считается рискованным.\n", $test->getName());
        }

        public function addSkippedTest(PHPUnit\Framework\Test $test, Exception $e, $time)
        {
            printf("Тест '%s' был пропущен.\n", $test->getName());
        }

        public function startTest(PHPUnit\Framework\Test $test)
        {
            printf("Тест '%s' запустился.\n", $test->getName());
        }

        public function endTest(PHPUnit\Framework\Test $test, $time)
        {
            printf("Тест '%s' завершился.\n", $test->getName());
        }

        public function startTestSuite(PHPUnit\Framework\TestSuite $suite)
        {
            printf("Набор тестов '%s' запустился.\n", $suite->getName());
        }

        public function endTestSuite(PHPUnit\Framework\TestSuite $suite)
        {
            printf("Набор тестов '%s' завершился.\n", $suite->getName());
        }
    }
    ?>

:numref:`extending-phpunit.examples.BaseTestListener.php`
показывает, как унаследоваться от абстрактного класса ``PHPUnit\Framework\BaseTestListener``,
который позволяет указать только интересующие методы интерфейса для вашего случая,
но при этом оставляет пустыми реализации всех остальных методов.

.. code-block:: php
    :caption: Использование базового слушателя тестов
    :name: extending-phpunit.examples.BaseTestListener.php

    <?php
    use PHPUnit\Framework\BaseTestListener;

    class ShortTestListener extends BaseTestListener
    {
        public function endTest(PHPUnit\Framework\Test $test, $time)
        {
            printf("Тест '%s' завершился.\n", $test->getName());
        }
    }
    ?>

В :ref:`appendixes.configuration.test-listeners` вы увидите,
как настроить PHPUnit для добавления слушателя тестов
к выполнению теста.

.. _extending-phpunit.PHPUnit_Framework_Test:

Реализация PHPUnit\Framework\Test
#################################

Интерфейс ``PHPUnit\Framework\Test`` узкий и простой
для реализации. Вы можете написать реализацию
``PHPUnit\Framework\Test``, которая проще, чем
``PHPUnit\Framework\TestCase``, и которая, например, запускает
*тесты, управляемые данными (data-driven tests)*.

:numref:`extending-phpunit.examples.DataDrivenTest.php`
показывает класс теста, управляемого данными, который сравнивает значения из файла
с значениями, разделённые запятой (Comma-Separated Values, CSV). Каждая строка такого файла выглядит как
``foo;bar``, где первое значение - это то, что мы ожидаем, а
второе значение - фактическое.

.. code-block:: php
    :caption: Тест, управляемый данными
    :name: extending-phpunit.examples.DataDrivenTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataDrivenTest implements PHPUnit\Framework\Test
    {
        private $lines;

        public function __construct($dataFile)
        {
            $this->lines = file($dataFile);
        }

        public function count()
        {
            return 1;
        }

        public function run(PHPUnit\Framework\TestResult $result = null)
        {
            if ($result === null) {
                $result = new PHPUnit\Framework\TestResult;
            }

            foreach ($this->lines as $line) {
                $result->startTest($this);
                PHP_Timer::start();
                $stopTime = null;

                list($expected, $actual) = explode(';', $line);

                try {
                    PHPUnit\Framework\Assert::assertEquals(
                      trim($expected), trim($actual)
                    );
                }

                catch (PHPUnit\Framework\AssertionFailedError $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addFailure($this, $e, $stopTime);
                }

                catch (Exception $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addError($this, $e, $stopTime);
                }

                if ($stopTime === null) {
                    $stopTime = PHP_Timer::stop();
                }

                $result->endTest($this, $stopTime);
            }

            return $result;
        }
    }

    $test = new DataDrivenTest('data_file.csv');
    $result = PHPUnit\TextUI\TestRunner::run($test);
    ?>

.. code-block:: bash

    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds

    There was 1 failure:

    1) DataDrivenTest
    Failed asserting that two strings are equal.
    expected string <bar>
    difference      <  x>
    got string      <baz>
    /home/sb/DataDrivenTest.php:32
    /home/sb/DataDrivenTest.php:53

    FAILURES!
    Tests: 2, Failures: 1.

.. _extending-phpunit.TestRunner:

Расширение TestRunner
#####################

PHPUnit |version| поддерживает расширения TestRunner, которые привязываются (hook)
к различным событиям во время выполнения теста.
См. :ref:`appendixes.configuration.extensions` для получения дополнительной информации
о регистрации расширений в конфигурационном XML-файле PHPUnit.

Каждое доступное событие, к которому может подключаться расширение, представлено интерфейсом,
которое расширению необходимо реализовать.
:ref:`extending-phpunit.hooks` перечисляет доступные события в
PHPUnit |version|.

.. _extending-phpunit.hooks:

Интерфейсы доступных событий
----------------------------

- ``AfterIncompleteTestHook``
- ``AfterLastTestHook``
- ``AfterRiskyTestHook``
- ``AfterSkippedTestHook``
- ``AfterSuccessfulTestHook``
- ``AfterTestErrorHook``
- ``AfterTestFailureHook``
- ``AfterTestWarningHook``
- ``BeforeFirstTestHook``
- ``BeforeTestHook``

:numref:`extending-phpunit.examples.TestRunnerExtension` показывает пример
расширения, реализующего ``BeforeFirstTestHook`` и ``AfterLastTestHook``:

.. code-block:: php
    :caption: Пример расширения TestRunner
    :name: extending-phpunit.examples.TestRunnerExtension

    <?php

    namespace Vendor;

    use PHPUnit\Runner\AfterLastTestHook;
    use PHPUnit\Runner\BeforeFirstTestHook;

    final class MyExtension implements BeforeFirstTestHook, AfterLastTestHook
    {
        public function executeAfterLastTest(): void
        {
            // вызывается после последнего выполненного теста
        }

        public function executeBeforeFirstTest(): void
        {
            // вызывается до выполнения первого теста
        }
    }
