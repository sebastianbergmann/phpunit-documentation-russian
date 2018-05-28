

.. _logging:

===========
Логирование
===========

PHPUnit может создавать несколько типов лог-файлов (logfiles).

.. _logging.xml:

Результаты теста (XML)
######################

Лог-файл XML результатов тестирования, созданный PHPUnit, основан на использовании
`задачи JUnit для Apache Ant <http://ant.apache.org/manual/Tasks/junit.html>`_.
В следующем примере показан лог-файл XML, сгенерированный для тестов в ``ArrayTest``:

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <testsuites>
      <testsuite name="ArrayTest"
                 file="/home/sb/ArrayTest.php"
                 tests="2"
                 assertions="2"
                 failures="0"
                 errors="0"
                 time="0.016030">
        <testcase name="testNewArrayIsEmpty"
                  class="ArrayTest"
                  file="/home/sb/ArrayTest.php"
                  line="6"
                  assertions="1"
                  time="0.008044"/>
        <testcase name="testArrayContainsAnElement"
                  class="ArrayTest"
                  file="/home/sb/ArrayTest.php"
                  line="15"
                  assertions="1"
                  time="0.007986"/>
      </testsuite>
    </testsuites>

Следующий лог-файл XML был сгенерирован для двух тестов
``testFailure`` и ``testError`` тестового класса
``FailureErrorTest`` и показывает как обозначаются неудачи
и ошибки.

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <testsuites>
      <testsuite name="FailureErrorTest"
                 file="/home/sb/FailureErrorTest.php"
                 tests="2"
                 assertions="1"
                 failures="1"
                 errors="1"
                 time="0.019744">
        <testcase name="testFailure"
                  class="FailureErrorTest"
                  file="/home/sb/FailureErrorTest.php"
                  line="6"
                  assertions="1"
                  time="0.011456">
          <failure type="PHPUnit\Framework\ExpectationFailedException">
    testFailure(FailureErrorTest)
    Failed asserting that &lt;integer:2&gt; matches expected value &lt;integer:1&gt;.

    /home/sb/FailureErrorTest.php:8
    </failure>
        </testcase>
        <testcase name="testError"
                  class="FailureErrorTest"
                  file="/home/sb/FailureErrorTest.php"
                  line="11"
                  assertions="0"
                  time="0.008288">
          <error type="Exception">testError(FailureErrorTest)
    Exception:

    /home/sb/FailureErrorTest.php:13
    </error>
        </testcase>
      </testsuite>
    </testsuites>

.. _logging.codecoverage.xml:

Покрытие кода (XML)
###################

Формат XML для логирования информации о покрытии, созданный PHPUnit,
отчасти основан на том, что используется в `Clover <http://www.atlassian.com/software/clover/>`_.

В следующем примере показан лог-файл XML, сгенерированный для тестов ``BankAccountTest``:

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <coverage generated="1184835473" phpunit="3.6.0">
      <project name="BankAccountTest" timestamp="1184835473">
        <file name="/home/sb/BankAccount.php">
          <class name="BankAccountException">
            <metrics methods="0" coveredmethods="0" statements="0"
                     coveredstatements="0" elements="0" coveredelements="0"/>
          </class>
          <class name="BankAccount">
            <metrics methods="4" coveredmethods="4" statements="13"
                     coveredstatements="5" elements="17" coveredelements="9"/>
          </class>
          <line num="77" type="method" count="3"/>
          <line num="79" type="stmt" count="3"/>
          <line num="89" type="method" count="2"/>
          <line num="91" type="stmt" count="2"/>
          <line num="92" type="stmt" count="0"/>
          <line num="93" type="stmt" count="0"/>
          <line num="94" type="stmt" count="2"/>
          <line num="96" type="stmt" count="0"/>
          <line num="105" type="method" count="1"/>
          <line num="107" type="stmt" count="1"/>
          <line num="109" type="stmt" count="0"/>
          <line num="119" type="method" count="1"/>
          <line num="121" type="stmt" count="1"/>
          <line num="123" type="stmt" count="0"/>
          <metrics loc="126" ncloc="37" classes="2" methods="4" coveredmethods="4"
                   statements="13" coveredstatements="5" elements="17"
                   coveredelements="9"/>
        </file>
        <metrics files="1" loc="126" ncloc="37" classes="2" methods="4"
                 coveredmethods="4" statements="13" coveredstatements="5"
                 elements="17" coveredelements="9"/>
      </project>
    </coverage>

.. _logging.codecoverage.text:

Code Coverage (TEXT)
####################

Человекочитаемое покрытие кода можно выводить в командую строку или текстовый файл.

Цель этого формата вывода - обеспечить общий обзор покрытия при работе
с небольшим набором классов. Для больших проектов этот вывод может быть
полезен для краткого обзора покрытия проектов или при использовании функциональности
с флагом ``--filter``.
При использовании из командной строки , записывая в ``php://stdout``,
будут учитываться настройка ``--colors``.

Запись в стандартный вывод - это опция по умолчанию при использовании из
командной строки. По умолчанию будут отображаться только файлы, имеющие
хотя бы одну покрытую строку. Это можно изменить через опцию конфигурации
``showUncoveredFiles``. См. :ref:`appendixes.configuration.logging`.
По умолчанию все файлы и их статус покрытия отображается в подробном отчёте.
Это можно изменить с помощью конфигурационной опции ``showOnlySummary``.
