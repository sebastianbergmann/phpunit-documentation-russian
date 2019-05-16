

.. _appendixes.configuration:

==========================
Конфигурационный XML-файл
==========================

.. _appendixes.configuration.phpunit:

PHPUnit
#######

Атрибуты элемента ``<phpunit>`` можно
использовать для настройки основной функциональности PHPUnit.

.. parsed-literal::

    <phpunit
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/\ |version|/phpunit.xsd"
             backupGlobals="true"
             backupStaticAttributes="false"
             <!--bootstrap="/path/to/bootstrap.php"-->
             cacheResult="false"
             cacheTokens="false"
             colors="false"
             convertErrorsToExceptions="true"
             convertNoticesToExceptions="true"
             convertWarningsToExceptions="true"
             forceCoversAnnotation="false"
             printerClass="PHPUnit\TextUI\ResultPrinter"
             <!--printerFile="/path/to/ResultPrinter.php"-->
             processIsolation="false"
             stopOnError="false"
             stopOnFailure="false"
             stopOnIncomplete="false"
             stopOnSkipped="false"
             stopOnRisky="false"
             testSuiteLoaderClass="PHPUnit\Runner\StandardTestSuiteLoader"
             <!--testSuiteLoaderFile="/path/to/StandardTestSuiteLoader.php"-->
             timeoutForSmallTests="1"
             timeoutForMediumTests="10"
             timeoutForLargeTests="60"
             verbose="false">
      <!-- ... -->
    </phpunit>

Конфигурация XML выше соответствует поведению по умолчанию
исполнителя тестов TextUI, описанному в :ref:`textui.clioptions`.

Дополнительные опции, недоступные в качестве опций командной строки:

``convertErrorsToExceptions``

    По умолчанию PHPUnit установит обработчик ошибок, которые преобразует
    следующие ошибки в исключения:

    - ``E_WARNING``

    - ``E_NOTICE``

    - ``E_USER_ERROR``

    - ``E_USER_WARNING``

    - ``E_USER_NOTICE``

    - ``E_STRICT``

    - ``E_RECOVERABLE_ERROR``

    - ``E_DEPRECATED``

    - ``E_USER_DEPRECATED``

    Установите ``convertErrorsToExceptions`` в
    ``false`` для отключения этой возможности.

``convertNoticesToExceptions``

    Когда установлено значение ``false``, обработчик ошибок, установленный
    ``convertErrorsToExceptions``, не будет преобразовывать ошибки
    ``E_NOTICE``, ``E_USER_NOTICE`` или
    ``E_STRICT`` в исключения.

``convertWarningsToExceptions``

    Когда установлено значение  ``false``, обработчик ошибок, установленный
    ``convertErrorsToExceptions``, не будет преобразовывать ошибки
    ``E_WARNING`` или ``E_USER_WARNING``
    в исключения.

``forceCoversAnnotation``

    Покрытие кода будет записываться только для тестов, в которых используется
    аннотация ``@covers``, задокументированная в
    :ref:`appendixes.annotations.covers`.

``timeoutForLargeTests``

    Если применяется ограничение по времени, основанное на размере теста, тогда этот атрибут
    устанавливает тайм-аут для всех тестов, отмеченных как ``@large``.
    Если тест не завершится в течение установленного тайм-аута, он завершится неудачей.

``timeoutForMediumTests``

    Если применяется ограничение по времени, основанное на размере теста, тогда этот атрибут
    устанавливает тайм-аут для всех тестов, отмеченных как ``@medium``.
    Если тест не завершится в течение установленного тайм-аута, он завершится неудачей.

``timeoutForSmallTests``

    Если применяется ограничение по времени, основанное на размере теста, тогда этот атрибут
    установит тайм-аут для всех тестов, не отмеченных как
    ``@medium`` или ``@large``.
    Если тест не завершится в течение установленного тайм-аута, он завершится неудачей.

.. _appendixes.configuration.testsuites:

Набор тестов
############

Элемент ``<testsuites>`` и его
один или несколько дочерних элементов ``<testsuite>`` можно
использовать для составления набора тестов из наборов тестов и тестовых классов.

.. code-block:: xml

    <testsuites>
      <testsuite name="Мой набор тестов">
        <directory>/path/to/*Test.php files</directory>
        <file>/path/to/MyTest.php</file>
        <exclude>/path/to/exclude</exclude>
      </testsuite>
    </testsuites>

Используя атрибуты ``phpVersion`` и
``phpVersionOperator`` можно указать требуемую версию PHP.
В приведённом ниже примере будут добавлены только файлы
:file:`/path/to/\*Test.php`
и файл :file:`/path/to/MyTest.php`, если версия PHP не менее 5.3.0.

.. code-block:: xml

      <testsuites>
        <testsuite name="My Test Suite">
          <directory suffix="Test.php" phpVersion="5.3.0" phpVersionOperator=">=">/path/to/files</directory>
          <file phpVersion="5.3.0" phpVersionOperator=">=">/path/to/MyTest.php</file>
        </testsuite>
      </testsuites>

Атрибут ``phpVersionOperator`` не является обязательным
и по умолчанию ``>=``.

.. _appendixes.configuration.groups:

Группы
######

Элемент ``<groups>`` и его дочерние элементы
``<include>``,
``<exclude>`` и
``<group>`` можно использовать для выбора
групп тестов, отмеченных аннотацией ``@group``
(описанных в :ref:`appendixes.annotations.group`),
которые должны (или не должны) выполняться.

.. code-block:: xml

    <groups>
      <include>
        <group>name</group>
      </include>
      <exclude>
        <group>name</group>
      </exclude>
    </groups>

Вышеприведённая конфигурация XML соответствует вызову исполнителя тестов TextUI
со следующими опциями:

-

  ``--group name``

-

  ``--exclude-group name``

.. _appendixes.configuration.whitelisting-files:

Файлы в белом списке для покрытия кода
######################################

Элемент ``<filter>`` и его дочерние элементы можно
использовать для настройки белого списка при создании отчёта о покрытии кода.

.. code-block:: xml

    <filter>
      <whitelist processUncoveredFilesFromWhitelist="true">
        <directory suffix=".php">/path/to/files</directory>
        <file>/path/to/file</file>
        <exclude>
          <directory suffix=".php">/path/to/files</directory>
          <file>/path/to/file</file>
        </exclude>
      </whitelist>
    </filter>

.. _appendixes.configuration.logging:

Логирование
###########

Элемент ``<logging>`` и его дочерние элементы ``<log>``
можно использовать для настройки логирования
выполнения тестов.

.. code-block:: xml

    <logging>
      <log type="coverage-html" target="/tmp/report" lowUpperBound="35"
           highLowerBound="70"/>
      <log type="coverage-clover" target="/tmp/coverage.xml"/>
      <log type="coverage-php" target="/tmp/coverage.serialized"/>
      <log type="coverage-text" target="php://stdout" showUncoveredFiles="false"/>
      <log type="junit" target="/tmp/logfile.xml"/>
      <log type="testdox-html" target="/tmp/testdox.html"/>
      <log type="testdox-text" target="/tmp/testdox.txt"/>
    </logging>

Вышеприведённая конфигурация XML соответствует вызову исполнителя тестов TextUI
со следующими опциями:

-

  ``--coverage-html /tmp/report``

-

  ``--coverage-clover /tmp/coverage.xml``

-

  ``--coverage-php /tmp/coverage.serialized``

-

  ``--coverage-text``

-

  ``> /tmp/logfile.txt``

-

  ``--log-junit /tmp/logfile.xml``

-

  ``--testdox-html /tmp/testdox.html``

-

  ``--testdox-text /tmp/testdox.txt``

Атрибуты ``lowUpperBound``, ``highLowerBound``,
``showUncoveredFiles`` не имеет эквивалента опции
исполнителя тестов TextUI.

-

  ``lowUpperBound``: Максимальный процент покрытия, который считается "низко" покрытым.

-

  ``highLowerBound``: Минимальный процент покрытия, который считается "высоко" покрытым.

-

  ``showUncoveredFiles``: Показать все файлы в белом списке при выводе с опцией ``--coverage-text``,
  а не только те, для которых есть информация о покрытии.

-

  ``showOnlySummary``: Показать только краткую сводку в выводе при использовании ``--coverage-text``.

.. _appendixes.configuration.test-listeners:

Обработчики тестов
##################

Элемент ``<listeners>`` и его дочерние элементы
``<listener>`` можно использовать для присоединения
дополнительных обработчиков теста к выполнению теста.

.. code-block:: xml

    <listeners>
      <listener class="MyListener" file="/optional/path/to/MyListener.php">
        <arguments>
          <array>
            <element key="0">
              <string>Sebastian</string>
            </element>
          </array>
          <integer>22</integer>
          <string>April</string>
          <double>19.78</double>
          <null/>
          <object class="stdClass"/>
        </arguments>
      </listener>
    </listeners>

Вышеприведённая конфигурация XML соответствует прикреплению объекта
``$listener`` (см. ниже) к выполнению теста:

.. code-block:: php

    $listener = new MyListener(
        ['Sebastian'],
        22,
        'April',
        19.78,
        null,
        new stdClass
    );

.. _appendixes.configuration.extensions:

Регистрация расширений TestRunner
#################################

Элемент ``<extensions>`` и его дочерние элементы ``<extension>``
можно использовать для регистрации пользовательских расширений TestRunner.

:numref:`configuration.examples.RegisterExtension` показывает, как
зарегистрировать такое расширение.

.. code-block:: xml
    :caption: Регистрация расширения TestRunner
    :name: configuration.examples.RegisterExtension

      <?xml version="1.0" encoding="UTF-8"?>
      <phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/7.1/phpunit.xsd">
          <extensions>
              <extension class="Vendor\MyExtension"/>
          </extensions>
      </phpunit>

.. _appendixes.configuration.php-ini-constants-variables:

Установка INI-настроек, констант и глобальных переменных PHP
############################################################

Элемент ``<php>`` и его дочерние элементы
можно использовать для настройки параметров, констант и глобальных переменных PHP.
Он может также использоваться для добавления новых путей в опцию ``include_path``.

.. code-block:: xml

    <php>
      <includePath>.</includePath>
      <ini name="foo" value="bar"/>
      <const name="foo" value="bar"/>
      <var name="foo" value="bar"/>
      <env name="foo" value="bar"/>
      <post name="foo" value="bar"/>
      <get name="foo" value="bar"/>
      <cookie name="foo" value="bar"/>
      <server name="foo" value="bar"/>
      <files name="foo" value="bar"/>
      <request name="foo" value="bar"/>
    </php>

Вышеприведённая конфигурация XML соответствует следующем коду PHP:

.. code-block:: php

    ini_set('foo', 'bar');
    define('foo', 'bar');
    $GLOBALS['foo'] = 'bar';
    $_ENV['foo'] = 'bar';
    $_POST['foo'] = 'bar';
    $_GET['foo'] = 'bar';
    $_COOKIE['foo'] = 'bar';
    $_SERVER['foo'] = 'bar';
    $_FILES['foo'] = 'bar';
    $_REQUEST['foo'] = 'bar';


