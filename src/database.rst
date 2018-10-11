

.. _database:

========================
Тестирование базы данных
========================

Многие примеры модульного тестирования начального и среднего уровня
на любом языке программирования предполагают, что с помощью простых тестов
можно легко протестировать логику приложения. Для приложений, ориентированных
на базы данных, это далеко от реальности. При начале использования, например,
WordPress, TYPO3 или Symfony с Doctrine или Propel,
вы легко столкнётесь с серьёзными проблемами с PHPUnit:
просто потому, что база данных тесно связана с этими библиотеками.

.. admonition:: Примечание

   Убедитесь, что у вас PHP-расширение ``pdo`` и расширения для
   баз данных, например ``pdo_mysql``, установлены.
   В противном приведённые ниже примеры не будут работать.

Вероятно, вам знакома такая ситуация из своей повседневной работы и проектов,
когда вы хотите применить свои новые или профессиональные навыки работы с PHPUnit,
но у вас возникла одна из следующих проблем:

#.

   Метод, который вы хотите протестировать довольно большую операцию JOIN и
   затем использует полученные данные для вычисления некоторых важных результатов.

#.

   В вашей бизнес-логике выполняются целый рад операторов SELECT, INSERT, UPDATE и
   DELETE.

#.

   Вам необходимо настроить тестовые данные (возможно, значительное количество) в более двух таблиц
   для получения подходящих первоначальных данных для тестируемых методов.

Расширение DbUnit значительно упрощает настройку базы данных для целей
тестирования и позволяет проверять содержимое базы данных после
выполнения ряда операций. Установка расширения DbUnit простая
и описана в :ref:`installation.optional-packages`.

.. _database.supported-vendors-for-database-testing:

Поддерживаемые поставщики для тестирования баз данных
#####################################################

В настоящее время DbUnit поддерживает MySQL, PostgreSQL, Oracle и SQLite. За счёт
интеграции в `Zend Framework <https://framework.zend.com>`_ или
`Doctrine 2 <https://www.doctrine-project.org>`_
это расширение имеет доступ к другим системам управления баз данных (СУБД), таким как IBM DB2 или
Microsoft SQL Server.

.. _database.difficulties-in-database-testing:

Трудности при тестировании баз данных
#####################################

Существует веская причина, почему все примеры модульного тестирования не включают
взаимодействие с базой данных: такого рода тесты одновременно сложны в настройке
и для поддержки. Во время тестирования с базой данных вам необходимо
позаботиться о следующих факторов:

-

  Схема и таблицы базы данных

-

  Вставка строк, необходимых для теста, в эти таблицы

-

  Проверка состояния базы данных после того, как тест был пройден

-

  Очистка базы данных для каждого нового теста

Поскольку многие API баз данных, такие как PDO, MySQLi или OCI8, громоздкие в
использовании и многословные при написании, выполнение этих шагов вручную может стать настоящим кошмаром.

Тестовый код должен быть как можно более коротким и точным по нескольким причинам:

-

  Вы не хотите изменять значительное количество тестового кода при
  небольших изменений в коде на продакшене.

-

  Вы хотите легко читать и понимать тестовый код, даже спустя
  несколько месяцев после его написания.

Кроме того, вы должны понимать, что база данных по существу является
глобальной переменной, вставленной в ваш код. Два теста в вашем тестовом наборе
могут работать с одной и той же базой данных, и, возможно, повторно использовать эти данные несколько
раз. Неудачи в одном тесте могут легко повлиять на результат последующих тестов, тем самым
затрудняя процесс тестирования. Ранее упомянутый этап очистки имеет большое значение для
решения проблемы "база данных — глобально введённая переменная".

DbUnit помогает упростить все эти проблемы при тестировании с базой данных элегантным способом.

С чем PHPUnit вам точно не сможет помочь, так это то, что тесты, использующие базу данных,
значительно медленнее по сравнению с тестами, которые её не используют.
В зависимости от того, насколько велико взаимодействие с базой данных, выполнение ваших тестов может занять
значительное количество времени. Однако, если вы храните небольшой объём данных, используемый для
каждого теста и пытаетесь протестировать как можно больше кода, который не взаимодействует с базой данных,
то на выполнение всех тестов займёт около одной минуту, даже на больших наборов тестов.

Например, набор тестов проекта `Doctrine 2 <https://www.doctrine-project.org>`_ в настоящее время
содержит около 1000 тестов, где почти половина из которых использует базу данных
и при этом всём выполнение тестов укладывается в 15 секунд, используя базу данных MySQL на стандартом
настольном компьютере.

.. _database.the-four-stages-of-a-database-test:

Четыре этапа теста базы данных
##############################

В своей книге "Шаблоны тестирования xUnit" (xUnit Test Patterns) Джерард Месарош (Gerard Meszaros) перечисляет четыре
этапа (стадии) модульного тестирования:

#.

   Настройка фикстуры

#.

   Выполнение системы тестирования (System Under Test)

#.

   Проверка результата

#.

   Очистка (teardown)

    *Что такое фикстура?*

    Фикстура описывает первоначальное состояние вашего приложения и базы данных
    в момент выполнения теста.

Тестирование базы данных требует, по крайней мере, установки и очистки,
чтобы очистить и записать необходимые данные фикстуры в ваши таблицы.
Тем не менее, у расширения базы данных есть веские основания для возврата
к четырём этапам при тестировании, использующем базу данных для формирования рабочего процесса,
выполняемого для каждого из тестов:

.. _database.clean-up-database:

1. Очистка базы данных
======================

Поскольку всегда есть первый тест, который работает с базой данных,
вы точно не знаете, есть ли в таблицах уже какие-нибудь данные.
PHPUnit выполнит операцию TRUNCATE для всех таблиц, чтобы вернуть их в пустое состояние.

.. _database.set-up-fixture:

2. Настройка фикстуры
=====================

Затем PHPUnit выполнит итерацию по всем указанным строкам фикстуры
и вставит их в соответствующие таблицы.

.. _database.run-test-verify-outcome-and-teardown:

3–5. Запуск теста, проверка результата и очистка
================================================

После того, как база данных сбрасывается и загружается с её изначальным состоянием,
текущий тест выполняется PHPUnit. Эта часть тестового кода не требует знание о
расширении базы данных вообще, вы можете продолжать и тестировать всё, что вам
нравится, с помощью вашего кода.

В вашем тесте используйте специальное утверждение ``assertDataSetsEqual()``
для целей проверки, однако, это совершенно необязательно. Эта возможность будет объяснена
в разделе «Утверждения базы данных».

.. _database.configuration-of-a-phpunit-database-testcase:

Конфигурация PHPUnit Database TestCase
######################################

Обычно при использовании PHPUnit ваши тесты наследуются от
``PHPUnit\Framework\TestCase`` следующим образом:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        public function testCalculate()
        {
            $this->assertSame(2, 1 + 1);
        }
    }

Если вы хотите протестировать код, который использует базу данных,
установка такого теста будет немного посложнее, потому что вам нужно
отнаследоваться от другого абстрактного класса TestCase,
требующего реализацию двух абстрактных методов
``getConnection()`` и ``getDataSet()``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        /**
         * @return PHPUnit\DbUnit\Database\Connection
         */
        public function getConnection()
        {
            $pdo = new PDO('sqlite::memory:');
            return $this->createDefaultDBConnection($pdo, ':memory:');
        }

        /**
         * @return PHPUnit\DbUnit\DataSet\IDataSet
         */
        public function getDataSet()
        {
            return $this->createFlatXMLDataSet(dirname(__FILE__).'/_files/guestbook-seed.xml');
        }
    }

.. _database.implementing-getconnection:

Реализация getConnection()
==========================

Для работы функциональности очистки и загрузки фикстур, расширение
базы данных PHPUnit требует доступа к соединению с базой данных,
которое абстрагируется между поставщиками и библиотекой PDO.
Важно отметить, что ваше приложение необязательно должно основываться
на PDO для использования расширения базы данных PHPUnit, подключение
просто используется для очистки и настройки фикстуры.

В предыдущем примере мы создаём подключение SQLite в памяти и передаём
его в метод ``createDefaultDBConnection``, который оборачивает экземпляр
PDO и второй параметр (имя базы данных) в очень простой уровень
абстракции с базой данных типа ``PHPUnit\DbUnit\Database\Connection``.

Раздел «Использование API подключения к базе данных» объясняет
API этого интерфейса и то, как вы можете наилучшим образом его использовать.

.. _database.implementing-getdataset:

Реализация getDataSet()
=======================

Метод ``getDataSet()`` определяет, каким должно быть первоначальное состояние
базы данных перед выполнением каждого теста.
Состояние базы данных абстрагируется с помощью двух концепций — DataSet и DataTable,
которые представлены интерфейсами ``PHPUnit\DbUnit\DataSet\IDataSet`` и
``PHPUnit\DbUnit\DataSet\IDataTable`` соответственно. В следующем разделе
будет подробно описано, как эти концепции работают и в чём их преимущества
при использовании их в тестировании базы данных.

Для реализации нам нужно только знать, что метод
``getDataSet()`` вызывается только один раз во время
``setUp()`` для извлечения набора данных фикстуры и
вставки его в базу данных. В этом примере мы используем фабричный
метод ``createFlatXMLDataSet($filename)``, который
представляет собой набор данных на основе XML-представления.

.. _database.what-about-the-database-schema-ddl:

Как насчёт схемы базы данных (Database Schema, DDL)?
====================================================

PHPUnit предполагает, что схема база данных со всеми её таблицами,
триггерами, последовательностями и представлениями создаётся до запуска теста.
Это означает, что вы как разработчик должны убедиться,
что ваша база данных правильно настроена перед выполнением набора тестов.

Существует несколько способов достижения этого предусловия для тестирования с базой данных.

#.

   Если вы используете базу данных с постоянным соединением (не SQLite в оперативной памяти),
   вы можете легко настроить базу данных один раз с помощью таких инструментов, как
   phpMyAdmin для MySQL, и повторно использовать базу данных при каждом запуске теста.

#.

   Если вы используете такие библиотеки как
   `Doctrine 2 <https://www.doctrine-project.org>`_ или
   `Propel <https://www.propelorm.org/>`_,
   вы можете использовать их API для создания схемы базы данных, который
   понадобиться всего один раз до запуска тестов. Вы можете использовать
   возможности `первоначальной (bootstrap) загрузки PHPUnit и конфигурации <textui.html>`_
   для выполнения этого кода каждый раз при выполнении тестов.

.. _database.tip-use-your-own-abstract-database-testcase:

Совет: Используйте собственную абстрактную реализацию PHPUnit Database TestCase
===============================================================================

Из предыдущего примера реализации вы легко можете увидеть, что метод
``getConnection()`` довольно статичен и может повторно использован
в различных тестовых классов с использованием базы данных. Кроме того, чтобы повысить
производительность тестов и снизить накладные расходы, связанные с базой данных,
вы можете немного провести рефакторинг кода для создания общего абстрактного класса для тестов
вашего приложения, который по-прежнему всё ещё позволяет указать другую фикстуру с данными
для каждого теста:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    abstract class MyApp_Tests_DatabaseTestCase extends TestCase
    {
        use TestCaseTrait;

        // инстанцировать только pdo один во время выполнения тестов для очистки/загрузки фикстуры
        static private $pdo = null;

        // инстанцировать только PHPUnit\DbUnit\Database\Connection один раз во время теста
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo === null) {
                    self::$pdo = new PDO('sqlite::memory:');
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, ':memory:');
            }

            return $this->conn;
        }
    }

Однако это соединение с базой данных жёстко закодировано в соединении PDO.
PHPUnit имеет одну удивительную возможность, которая поможет сделать этот тестовый класс
ещё более универсальным. Если вы используете
`XML-конфигурацию <appendixes.configuration.html#appendixes.configuration.php-ini-constants-variables>`_,
вы можете сделать подключение к базе данных настраиваемым для каждого запуска теста.
Сначала давайте создадим файл "phpunit.xml" в тестовом каталоге tests/
приложения со следующим содержимым:

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8" ?>
    <phpunit>
        <php>
            <var name="DB_DSN" value="mysql:dbname=myguestbook;host=localhost" />
            <var name="DB_USER" value="user" />
            <var name="DB_PASSWD" value="passwd" />
            <var name="DB_DBNAME" value="myguestbook" />
        </php>
    </phpunit>

Теперь мы можем изменить тестовый класс, чтобы он выглядел так:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    abstract class Generic_Tests_DatabaseTestCase extends TestCase
    {
        use TestCaseTrait;

        // инстанцировать только pdo один во время выполнения тестов для очистки/загрузки фикстуры
        static private $pdo = null;

        // инстанцировать только PHPUnit\DbUnit\Database\Connection один раз во время теста
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo === null) {
                    self::$pdo = new PDO( $GLOBALS['DB_DSN'], $GLOBALS['DB_USER'], $GLOBALS['DB_PASSWD'] );
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, $GLOBALS['DB_DBNAME']);
            }

            return $this->conn;
        }
    }

Теперь мы можем запустить набор тестов базы данных, используя различные
конфигурации из интерфейса командной строки:

.. code-block:: bash

    $ user@desktop> phpunit --configuration developer-a.xml MyTests/
    $ user@desktop> phpunit --configuration developer-b.xml MyTests/

Возможность легко запускать тесты, использующие базу данных, с различными
конфигурациями очень важно, если вы ведёте разработку на компьютере разработчика.
Если несколько разработчиков выполняют тесты базы данных, используя одно и то же
соединение с базой данных, то вы запросто можете столкнуться с неудачами выполнения тестов из-за
состояния гонки (race-conditions).

.. _database.understanding-datasets-and-datatables:

Понимание DataSets и DataTables
###############################

Ключевой концепцией расширения базы данных PHPUnit являются DataSets и
DataTables. Вы должны попытаться понять эту простую концепцию для освоения
тестирования с использованием базы данных с помощью PHPUnit. DataSet и DataTable —
это уровни абстракции вокруг строк и столбцов баз данных.
Простой API скрывает основное содержимое базы данных в структуре объекта,
который также может быть реализован другими источниками, отличными от
базы данных.

Эта абстракция необходима для сравнения текущего содержимого
базы данных с ожидаемым. Ожидаемое содержимое может быть
представлено в виде файлов формата XML, YAML, CSV или массива PHP, например.
Интерфейсы DataSet и DataTable позволяют сравнивать эти
концептуально разные источники путём эмуляции хранилища реляционных баз данных
в семантически подобном подходе.

Рабочий процесс для утверждений базы данных в ваших тестах, таким образом, состоит из
трёх простых шагов:

-

  Указать одну или более таблиц в базе данных по имени таблицы (фактический
  набор данных)

-

  Указать ожидаемый набор данных в предпочтительном формате (YAML, XML, ..)

-

  Проверить утверждение, что оба представления набора данных равны друг другу (эквивалентны).

Утверждения это не единственный вариант использования для DataSet и DataTable
в расширении базы данных PHPUnit. Как показано в предыдущем разделе,
они также описывают первоначальное содержимое базы данных.
Вы вынуждены определять набор данных фикстуры в Database TestCase,
который затем используется для:

-

  Удаления всех строк из таблиц, указанных в наборе данных.

-

  Записи всех строк в таблицы данных в базе данных.

.. _database.available-implementations:

Доступные реализации
====================

Существует три различных типов наборов данных/таблиц данных:

-

  DataSets и DataTables на основе файлов

-

  DataSet и DataTable на основе запросов

-

  Фильтр и объединение DataSets и DataTables

Файловые наборы данных и таблиц обычно используются для
первоначальной фикстуры и описывают ожидаемое состояние базы данных.

.. _database.flat-xml-dataset:

Flat XML DataSet
----------------

Наиболее распространённый набор называется Flat XML. Это очень простой (flat) XML-формат,
где тег внутри корневого узла ``<dataset>`` представляет ровно одну строку в базе данных.
Имена тегов соответствуют таблице, куда будут добавляться строки (записи), а
атрибуты тега представляют столбцы записи. Пример для приложения простой гостевой книги
мог бы выглядеть подобным образом:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
    </dataset>

Это, очевидно, легко писать. В этом примере
``<guestbook>`` — имя таблицы, в которую добавляются две строки
с четырьмя столбцами "id", "content", "user" и "created" с соответствующими им значениями.

Однако за эту простоту приходиться платить.

Из предыдущего примера неочевидно, как указать пустую таблицу.
Вы можете вставить тег без атрибутов с именем пустой таблицы.
Тогда такой XML-файл для пустой таблицы гостевой книги будет выглядеть так:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook />
    </dataset>

Обработка значений NULL в простых наборах данных XML утомительна.
Значение NULL отличается от пустого строкового значения почти в любой
базе данных (Oracle — исключение), что трудно описать
в обычном формате XML. Вы можете представить значение NULL,
опуская атрибут из строки (записи). Если наша гостевая книга
разрешает анонимные записи, представленные значением NULL в столбце
"user", гипотетическое состояние таблицы гостевой книги может быть таким:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Привет, дружище!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="Мне нравится это!" created="2010-04-26 12:14:20" />
    </dataset>

В нашем случае вторая запись добавлена анонимна. Однако это
приводит к серьёзной проблеме определения столбцов. Во время утверждений
о равенстве данных каждый набор данных должен указывать, какие столбцы
хранятся в таблице. Если атрибут указан NULL для всех строк таблицы данных,
как расширение базы данных определит, что столбец должен быть частью таблицы?

Обычный набор данных XML делает сейчас решающе важное предположение, объявляя, что
атрибуты в первой определённой строке таблицы определяют столбцы
этой таблицы. В предыдущем примере это означало бы, что
"id", "content“, "user" и "created" будет столбцами таблицы гостевой книги. Для
второй строки, где пользователь ("user") не определён, в базу данных в столбец "user"
будет вставлено значение NULL.

Когда первая запись гостевой книги удаляется из набора данных, только
"id", "content" и "created" будут столбцами таблицы гостевой книги,
поскольку столбец "user" не определён.

Чтобы эффективно использовать набор данных Flat XML, когда значения NULL
имеют важное значение, первая строка каждой таблицы не должна содержать значения NULL,
и только последующие строки могут пропускать атрибуты.
Это может быть неудобно, поскольку порядок строк является значимым фактором
для утверждений базы данных.

В свою очередь, если вы укажете только подмножество столбцов таблицы в наборе данных
Flat XML, все пропущенные значения будут установлены в значения по умолчанию.
Это приведёт к ошибкам, только если один из пропущенных столбцов определён как
"NOT NULL DEFAULT NULL".

В заключение я могу только посоветовать использовать наборы данных Flat XML, только если вам
не нужны значения NULL.

Вы можете создать экземпляр обычного набора данных XML внутри Database TestCase, вызвав метод
``createFlatXmlDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
        }
    }

.. _database.xml-dataset:

XML DataSet
-----------

Есть ещё один структурированный набор данных XML, который немного более многословный
при записи, но не имеет проблем с NULL-значениями из набора данных Flat XML.
Внутри корневого узла ``<dataset>`` вы можете указать теги ``<table>``,
``<column>``, ``<row>``,
``<value>`` и
``<null />``.
Эквивалентный набор данных для ранее определённой гостевой книги с использованием Flat XML, будет выглядеть так:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <table name="guestbook">
            <column>id</column>
            <column>content</column>
            <column>user</column>
            <column>created</column>
            <row>
                <value>1</value>
                <value>Привет, дружище!</value>
                <value>joe</value>
                <value>2010-04-24 17:15:23</value>
            </row>
            <row>
                <value>2</value>
                <value>Мне нравится это!</value>
                <null />
                <value>2010-04-26 12:14:20</value>
            </row>
        </table>
    </dataset>

Любой определённый тег ``<table>`` имеет имя и требует
определение всех столбцов с их именами. Он может содержать ноль
или любое положительное число вложенных элементов ``<row>``.
Отсутствие элементов ``<row>``означает, что таблица пуста.
Теги ``<value>`` и ``<null />`` должны быть указаны в порядке,
ранее заданных элементов ``<column>``. Тег ``<null />``, очевидно,
означает, что значение равно NULL.

Вы можете создать экземпляр набора данных XML внутри
Database TestCase, вызвав метод ``createXmlDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createXMLDataSet('myXmlFixture.xml');
        }
    }

.. _database.mysql-xml-dataset:

MySQL XML DataSet
-----------------

Этот новый XML-формат специально предназначен для
`сервера баз данных MySQL <https://www.mysql.com>`_.
Его поддержка была добавлена в PHPUnit 3.5. Файлы в этом формате могут
быть сгенерированы с помощью утилиты
`mysqldump <https://dev.mysql.com/doc/refman/5.0/en/mysqldump.html>`_.
В отличие от наборов данных CSV, которые ``mysqldump`` также
поддерживает, один файл в этом XML-формате может содержать данные
для нескольких таблиц. Вы можете создать файл в этом формате, запустив
``mysqldump`` следующим образом:

.. code-block:: bash

    $ mysqldump --xml -t -u [username] --password=[password] [database] > /path/to/file.xml

Этот файл можно использовать в вашем Database TestCase, путём вызова
метода ``createMySQLXMLDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createMySQLXMLDataSet('/path/to/file.xml');
        }
    }

.. _database.yaml-dataset:

YAML DataSet
------------

Кроме того, вы можете использовать набор данных YAML для примера гостевой книги:

.. code-block:: bash

    guestbook:
      -
        id: 1
        content: "Привет, дружище!"
        user: "joe"
        created: 2010-04-24 17:15:23
      -
        id: 2
        content: "Мне нравится это!"
        user:
        created: 2010-04-26 12:14:20

Этот формат прост и удобен, а главное он решает проблему с NULL в похожем наборе данных Flat XML.
NULL в YAML — это просто имя столбца без указанного значения. Пустая строка указывается таким образом —
``column1: ""``.

В настоящее время набор данных YAML не имеет фабричного метода в Database TestCase,
поэтому вам необходимо создать его самим:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;
    use PHPUnit\DbUnit\DataSet\YamlDataSet;

    class YamlGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            return new YamlDataSet(dirname(__FILE__)."/_files/guestbook.yml");
        }
    }

.. _database.csv-dataset:

CSV DataSet
-----------

Ещё один файловый набор данных на основе формата CSV. Каждая таблица
набора данных представлена одним CSV-файлом. Для нашего примера с гостевой книгой
мы определяем файл guestbook-table.csv:

.. code-block:: bash

    id,content,user,created
    1,"Привет, дружище!","joe","2010-04-24 17:15:23"
    2,"Мне нравится это!","nancy","2010-04-26 12:14:20"

Хотя это очень удобно для редактирования через Excel или OpenOffice,
вы не можете указать значения NULL в наборе данных CSV. Пустой
столбец приведёт к тому, что в столбец в базе данных будет вставлено пустое значение.

Вы можете создать CSV DataSet следующим образом:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;
    use PHPUnit\DbUnit\DataSet\CsvDataSet;

    class CsvGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            $dataSet = new CsvDataSet();
            $dataSet->addTable('guestbook', dirname(__FILE__)."/_files/guestbook.csv");
            return $dataSet;
        }
    }

.. _database.array-dataset:

Array DataSet
-------------

В расширении базы данных PHPUnit не существует (пока) массива на основе DataSet,
но мы может легко реализовать свой собственный. Пример гостевой книги должен выглядеть так:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ArrayGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            return new MyApp_DbUnit_ArrayDataSet(
                [
                    'guestbook' => [
                        [
                            'id' => 1,
                            'content' => 'Привет, дружище!',
                            'user' => 'joe',
                            'created' => '2010-04-24 17:15:23'
                        ],
                        [
                            'id' => 2,
                            'content' => 'Мне нравится это!',
                            'user' => null,
                            'created' => '2010-04-26 12:14:20'
                        ],
                    ],
                ]
            );
        }
    }

DataSet PHP имеет очевидные преимущества перед всеми другими наборами данных на основе файлов:

-

  Массивы PHP, очевидно, могут обрабатывать значения ``NULL``.

-

  Вам не нужны дополнительные файлы для утверждений, и вы можете непосредственно использовать
  их в TestCase.

Чтобы этот набор выглядел как Flat XML, CSV или YAML, ключи
первой указанной строки определяют имена столбцов таблицы, в
предыдущем случае это были бы "id", "content", "user" и "created".

Реализация массива DataSet проста и понятна:

.. code-block:: php

    <?php

    use PHPUnit\DbUnit\DataSet\AbstractDataSet;
    use PHPUnit\DbUnit\DataSet\DefaultTableMetaData;
    use PHPUnit\DbUnit\DataSet\DefaultTable;
    use PHPUnit\DbUnit\DataSet\DefaultTableIterator;

    class MyApp_DbUnit_ArrayDataSet extends AbstractDataSet
    {
        /**
         * @var array
         */
        protected $tables = [];

        /**
         * @param array $data
         */
        public function __construct(array $data)
        {
            foreach ($data AS $tableName => $rows) {
                $columns = [];
                if (isset($rows[0])) {
                    $columns = array_keys($rows[0]);
                }

                $metaData = new DefaultTableMetaData($tableName, $columns);
                $table = new DefaultTable($metaData);

                foreach ($rows as $row) {
                    $table->addRow($row);
                }
                $this->tables[$tableName] = $table;
            }
        }

        protected function createIterator($reverse = false)
        {
            return new DefaultTableIterator($this->tables, $reverse);
        }

        public function getTable($tableName)
        {
            if (!isset($this->tables[$tableName])) {
                throw new InvalidArgumentException("$tableName не является таблицей в текущей базе данных.");
            }

            return $this->tables[$tableName];
        }
    }

.. _database.query-sql-dataset:

Query (SQL) DataSet
-------------------

Для утверждений базы данных вам нужен не только набор данный на основе файлов,
но также набор данных на основе запросов (Query)/SQL, содержащий фактическое содержимое
базы данных. Здесь показан Query DataSet:

.. code-block:: php

    <?php
    $ds = new PHPUnit\DbUnit\DataSet\QueryDataSet($this->getConnection());
    $ds->addTable('guestbook');

Добавление таблицы просто по имени — это неявный способ определения
таблицы данных со следующим запросом:

.. code-block:: php

    <?php
    $ds = new PHPUnit\DbUnit\DataSet\QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT * FROM guestbook');

Вы можете использовать его, указав произвольные запросы для своих
таблиц, например, ограничивая количество строк, столбцов или добавление
предложение ``ORDER BY``:

.. code-block:: php

    <?php
    $ds = new PHPUnit\DbUnit\DataSet\QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT id, content FROM guestbook ORDER BY created DESC');

В разделе «Утверждения базы данных» будет приведена подробная информация о том,
как использовать Query DataSet.

.. _database.database-db-dataset:

Database (DB) Dataset
---------------------

При доступе к тестовому подключению вы можете автоматически создать
DataSet, который состоит из всех таблиц с их содержимым в базе
данных, указанной в качестве второго параметра, для фабричного метода соединений.

Вы можете либо создать набор данных для полной базы данных, как показано
в ``testGuestbook()``, либо ограничится набором
указанных имён таблиц с помощью белого списка, как показано в методе
``testFilteredGuestbook()``.

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MySqlGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        /**
         * @return PHPUnit\DbUnit\Database\Connection
         */
        public function getConnection()
        {
            $database = 'my_database';
            $user = 'my_user';
            $password = 'my_password';
            $pdo = new PDO('mysql:...', $user, $password);
            return $this->createDefaultDBConnection($pdo, $database);
        }

        public function testGuestbook()
        {
            $dataSet = $this->getConnection()->createDataSet();
            // ...
        }

        public function testFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet($tableNames);
            // ...
        }
    }

.. _database.replacement-dataset:

Замена DataSet
--------------

Я говорил о проблемах с NULL в наборах данных Flat XML и CSV,
но есть несколько сложное обходное решение для получения
обоих наборов данных, работающих с NULL.

Замена DataSet — декоратор для существующего набора данных, позволяющий
заменять значения в любом столбце набора данных другим заменяющим значением.
Для получения примера нашей гостевой книги, работающим со значениями NULL,
мы указываем файл следующим образом:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

Затем мы оборачиваем Flat XML DataSet в Replacement DataSet:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ReplacementTest extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            $ds = $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
            $rds = new PHPUnit\DbUnit\DataSet\ReplacementDataSet($ds);
            $rds->addFullReplacement('##NULL##', null);
            return $rds;
        }
    }

.. _database.dataset-filter:

DataSet Filter
--------------

Если у вас большой файл фикстуры, вы можете использовать фильтрацию набора данных для
создания белого и чёрного списка таблиц и столбцов, которые должны содержаться
поднаборе. Это особенно удобно в сочетании с DB DataSet для фильтрации столбцов набора данных.

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class DataSetFilterTest extends TestCase
    {
        use TestCaseTrait;

        public function testIncludeFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit\DbUnit\DataSet\DataSetFilter($dataSet);
            $filterDataSet->addIncludeTables(['guestbook']);
            $filterDataSet->setIncludeColumnsForTable('guestbook', ['id', 'content']);
            // ..
        }

        public function testExcludeFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit\DbUnit\DataSet\DataSetFilter($dataSet);
            $filterDataSet->addExcludeTables(['foo', 'bar', 'baz']); // only keep the guestbook table!
            $filterDataSet->setExcludeColumnsForTable('guestbook', ['user', 'created']);
            // ..
        }
    }

.. admonition:: Примечание

    Вы не можете одновременно использовать исключение и включение фильтрации столбцов на одной и той же таблице,
    только на разных. Кроме того, это возможно только для таблиц белого или чёрного списка, а не для обоих.

.. _database.composite-dataset:

Составной DataSet
-----------------

Составной DataSet очень полезен для объединения (агрегирования) нескольких
уже существующих наборов данных в один набор данных. Когда несколько
наборов данных содержат одну и ту же таблицу, строки добавляются
в указанном порядке. Например, если у нас есть два набора данных —
*fixture1.xml*:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Привет, дружище!" user="joe" created="2010-04-24 17:15:23" />
    </dataset>

и *fixture2.xml*:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="2" content="Мне нравится это!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

Используя составной DataSet, мы можем объединить оба файла фикстуры:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class CompositeTest extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            $ds1 = $this->createFlatXmlDataSet('fixture1.xml');
            $ds2 = $this->createFlatXmlDataSet('fixture2.xml');

            $compositeDs = new PHPUnit\DbUnit\DataSet\CompositeDataSet();
            $compositeDs->addDataSet($ds1);
            $compositeDs->addDataSet($ds2);

            return $compositeDs;
        }
    }

.. _database.beware-of-foreign-keys:

Остерегайтесь внешних ключей
============================

Во время установки фикстуры расширения базы данных, PHPUnit вставляет строки
в базу данных в том порядке, в котором они указаны в вашей фикстуре.
Если ваша схема базы данных использует внешние ключи, это означает, что вы должны
указывать таблицы в порядке, не вызывающем нарушений ограничений внешних ключей.

.. _database.implementing-your-own-datasetsdatatables:

Реализация собственного DataSets/DataTables
===========================================

Для понимания внутренностей DataSets и DataTables, давайте
взглянем на интерфейс DataSet. Вы можете пропустить эту часть,
если не планируете реализовать собственный DataSet или DataTable.

.. code-block:: php

    <?php
    namespace PHPUnit\DbUnit\DataSet;

    interface IDataSet extends IteratorAggregate
    {
        public function getTableNames();
        public function getTableMetaData($tableName);
        public function getTable($tableName);
        public function assertEquals(IDataSet $other);

        public function getReverseIterator();
    }

Общедоступный интерфейс используется внутри утверждения
``assertDataSetsEqual()`` в Database TestCase для проверки качества набора данных.
Из интерфейса ``IteratorAggregate`` IDataSet наследует метод ``getIterator()`` для итерации
по всем таблицах набора данных. Обратный итератор позволяет PHPUnit
очистить строки таблицы, противоположные порядку их создания для удовлетворения ограничений
внешнего ключа.

В зависимости от реализации применяются различные подходы для добавления
экземпляров таблиц в набор данных. Например, таблицы добавляются
внутри структуры во время создания из исходного файла во все
файловые наборы данных, таких как ``YamlDataSet``,
``XmlDataSet`` или ``FlatXmlDataSet``.

Таблица также представлена следующим интерфейсом:

.. code-block:: php

    <?php
    namespace PHPUnit\DbUnit\DataSet;

    interface ITable
    {
        public function getTableMetaData();
        public function getRowCount();
        public function getValue($row, $column);
        public function getRow($row);
        public function assertEquals(ITable $other);
    }

За исключением метода ``getTableMetaData()``, который говорит сам за себя.
Используемые методы необходимы для различных утверждений расширения базы данных, которые
поясняются в следующей главе. Метод ``getTableMetaData()`` должен возвращать реализацию
интерфейса ``PHPUnit\DbUnit\DataSet\ITableMetaData``, который описывает структуру таблицы.
В нём содержится следующая информация:

-

  Имя таблицы

-

  Массив имён столбцов таблицы, упорядоченных по их появлению
  в результирующем наборе.

-

  Массив столбцов первичных ключей.

Этот интерфейс также имеет утверждение, которое проверяет, равны ли
два экземпляра табличных метаданных (Table Metadata) друг другу, которое используется
утверждением равенства набора данных.

.. _database.the-connection-api:

Использование API подключения к базе данных
###########################################

В интерфейсе Connection есть три интересных метода, которые
необходимо вернуть из метода ``getConnection()`` в Database TestCase:

.. code-block:: php

    <?php
    namespace PHPUnit\DbUnit\Database;

    interface Connection
    {
        public function createDataSet(array $tableNames = null);
        public function createQueryTable($resultName, $sql);
        public function getRowCount($tableName, $whereClause = null);

        // ...
    }

#.

   Метод ``createDataSet()`` создаёт набор данных базы данных (Database (DB) DataSet),
   как описано в разделе реализации DataSet.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateDataSet()
           {
               $tableNames = ['guestbook'];
               $dataSet = $this->getConnection()->createDataSet();
           }
       }

#.

   Метод ``createQueryTable()`` может использоваться
   для создания экземпляров QueryTable, передавая им имя результат и SQL-запроса.
   Это удобный метод, когда дело доходит до утверждений результата/таблицы,
   как будет показано в следующем разделе «API утверждений базы данных».

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateQueryTable()
           {
               $tableNames = ['guestbook'];
               $queryTable = $this->getConnection()->createQueryTable('guestbook', 'SELECT * FROM guestbook');
           }
       }

#.

   Метод ``getRowCount()`` — это удобный способ получения доступа к количеству
   строк в таблице, необязательно отфильтрованное дополнительным
   предложением where. Это можно использовать с простым утверждением равенства:

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testGetRowCount()
           {
               $this->assertSame(2, $this->getConnection()->getRowCount('guestbook'));
           }
       }

.. _database.database-assertions-api:

API утверждений базы данных
###########################

Инструмент тестирования расширения базы данных, безусловно, содержит
утверждения, которые вы можете использовать для проверки текущего состояния базы данных,
таблиц и подсчёта строк таблиц. В этом разделе подробно описывается
эта функциональность:

.. _database.asserting-the-row-count-of-a-table:

Утверждение количество строк таблицы
====================================

Часто бывает полезно проверить, содержит ли таблица определённое количество строк.
Вы можете легко достичь этого без дополнительного кода, используя
API Connection. Предположим, мы хотим проверить, что после вставки
строк в нашу гостевую книгу мы имеем не только две первоначальные записи,
которые были во всех предыдущих примерах, но а также третью, только что добавленную:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class GuestbookTest extends TestCase
    {
        use TestCaseTrait;

        public function testAddEntry()
        {
            $this->assertSame(2, $this->getConnection()->getRowCount('guestbook'), "Pre-Condition");

            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $this->assertSame(3, $this->getConnection()->getRowCount('guestbook'), "Inserting failed");
        }
    }

.. _database.asserting-the-state-of-a-table:

Утверждение состояния таблицы
=============================

Предыдущее утверждение полезно, но мы обязательно хотим проверить
фактическое содержимое таблицы, чтобы убедиться, что все значения были
записаны в соответствующие столбцы. Это может быть достигнуто с помощью утверждения
таблицы.

Для этого нам нужно определить экземпляр таблицы запроса (Query Table), который выводит
содержимое по имени таблицы и SQL-запроса и сравнивает его с набором данных на основе файлов/массивов:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class GuestbookTest extends TestCase
    {
        use TestCaseTrait;

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

Теперь для этого утверждения мы должны создать обычный XML-файл *expectedBook.xml*:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Привет, дружище!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="Мне нравится это!" user="nancy" created="2010-04-26 12:14:20" />
        <guestbook id="3" content="Привет, мир!" user="suzy" created="2010-05-01 21:47:08" />
    </dataset>

Это утверждение будет успешным только в том случае, если оно будет запущено точно в *2010–05–01 21:47:08*.
Даты представляют собой особую проблему при тестировании с использованием базы данных, и мы может обойти
эту ошибку, опуская столбец "created" в утверждении.

Скорректированный файл Flat XML *expectedBook.xml*, вероятно, теперь
должен выглядеть следующим образом для прохождения утверждения:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Привет, дружище!" user="joe" />
        <guestbook id="2" content="Мне нравится это!" user="nancy" />
        <guestbook id="3" content="Привет, мир!" user="suzy" />
    </dataset>

Мы должны исправить вызов таблицы запроса (Query Table):

.. code-block:: php

    <?php
    $queryTable = $this->getConnection()->createQueryTable(
        'guestbook', 'SELECT id, content, user FROM guestbook'
    );

.. _database.asserting-the-result-of-a-query:

Утверждение результата запроса
==============================

Вы также можете утверждать результат сложных запросов с помощью подхода Query
Table, просто указав имя результата с запросом и сравнивая его с набором данным:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ComplexQueryTest extends TestCase
    {
        use TestCaseTrait;

        public function testComplexQuery()
        {
            $queryTable = $this->getConnection()->createQueryTable(
                'myComplexQuery', 'SELECT complexQuery...'
            );
            $expectedTable = $this->createFlatXmlDataSet("complexQueryAssertion.xml")
                                  ->getTable("myComplexQuery");
            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }

.. _database.asserting-the-state-of-multiple-tables:

Утверждение состояния нескольких таблиц
=======================================

Конечно, вы можете утверждать состояние одновременно нескольких таблиц
и  сравнивать запрос набора результата с файловым набором данных. Для утверждений
DataSet существует два разных способа.

#.

   Вы можете использовать базу данных (Database, DB) DataSet из Connection и сравнить
   её с набором данных на основе файлов.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class DataSetAssertionsTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateDataSetAssertion()
           {
               $dataSet = $this->getConnection()->createDataSet(['guestbook']);
               $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');
               $this->assertDataSetsEqual($expectedDataSet, $dataSet);
           }
       }

#.

   Вы можете создать DataSet самостоятельно:

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;
       use PHPUnit\DbUnit\DataSet\QueryDataSet;

       class DataSetAssertionsTest extends TestCase
       {
           use TestCaseTrait;

           public function testManualDataSetAssertion()
           {
               $dataSet = new QueryDataSet();
               $dataSet->addTable('guestbook', 'SELECT id, content, user FROM guestbook'); // additional tables
               $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');

               $this->assertDataSetsEqual($expectedDataSet, $dataSet);
           }
       }

.. _database.frequently-asked-questions:

Часто задаваемые вопросы
########################

.. _database.will-phpunit-re-create-the-database-schema-for-each-test:

Будет ли PHPUnit (повторно) создавать схему базу данных для каждого теста?
==========================================================================

Нет, PHPUnit требует, чтобы все объекты базы данных были доступны при запуске набора.
Перед запуском набора тестов необходимо создать базу данных, таблицы, последовательности, триггеры и представления.

У `Doctrine 2 <https://www.doctrine-project.org>`_ или
`eZ Components <http://www.ezcomponents.org>`_ есть
мощные инструменты, которые позволяют вам создать схему базу данных из
предопределённых структур данных. Однако они должны подключены к расширению
PHPUnit, чтобы разрешить автоматическое восстановление базы данных
до запуска всего полного набора тестов.

Поскольку каждый тест полностью очищает базу данных, вам даже не требуется
пересоздавать базу данных для каждого запуска теста. Постоянно
доступная база данных работает отлично.

.. _database.am-i-required-to-use-pdo-in-my-application-for-the-database-extension-to-work:

Необходимо ли мне обязательно использовать PDO в моём приложении для расширения базы данных?
============================================================================================

Нет, PDO требуется только для очистки и установки фикстуры, а также для
утверждений. Вы можете использовать любую понравившуюся абстракцию базы данных внутри
своего кода.

.. _database.what-can-i-do-when-i-get-a-too-much-connections-error:

Что мне делать, когда я получаю ошибку "Too much Connections"?
==============================================================

Если вы не кешируете экземпляр PDO, созданный через метод
TestCase ``getConnection()``, количество подключений
к базе данных увеличивается на один или несколько при каждом
тесте базы данных. По умолчанию конфигурация MySQL позволяет только 100
одновременных подключений, у других поставщиков также имеются свои ограничения
на количество максимальных подключений.

Подраздел
«Используйте собственную реализацию PHPUnit Abstract Database TestCase» показывает, как
можно предотвратить эту ошибку, используя один закешированный экземпляр PDO во всех ваших тестов.

.. _database.how-to-handle-null-with-flat-xml-csv-datasets:

Как обрабатывать NULL в наборах данных Flat XML / CSV?
======================================================

Не делайте этого. Вместо этого вы должны использовать наборы данных XML или YAML.
