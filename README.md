# Документация PHPUnit на русском языке

## Переводы

Каждый перевод документации поддерживается в отдельном репозитории:

* [Документация на английском языке](https://github.com/sebastianbergmann/phpunit-documentation-english)
* [Документация на испанском языке](https://github.com/sebastianbergmann/phpunit-documentation-spanish)
* [Документация на французском языке](https://github.com/sebastianbergmann/phpunit-documentation-french)
* [Документация на португальском языке](https://github.com/sebastianbergmann/phpunit-documentation-brazilian-portuguese)
* [Документация на японском языке](https://github.com/sebastianbergmann/phpunit-documentation-japanese)
* [Документация на китайском упрощённом языке](https://github.com/sebastianbergmann/phpunit-documentation-chinese)

### Добавление нового перевода

Если вы хотите создать новый перевод, пожалуйста, откройте ишью в
трекере английской документации, в которой укажите, на какой язык вы хотели бы
перевести документацию. Будет создан репозиторий и добавлен к доступным переводам.

В идеале вы уже подготовили первую версию перевода в форке или
копии английской документации, которая затем будет импортирована в
официальный репозиторий.

## Сборка документации

### Требования

- Python
- [Sphinx](http://www.sphinx-doc.org/)
- [Тема для Sphinx «Read the Docs»](https://github.com/rtfd/sphinx_rtd_theme)

### Сборка документации в формате HTML

Чтобы выполнить полную сборку документации:

```bash
make html
```

### Вывод

После выполнения вышеуказанной команды этого вы найдете HTML-файлы по пути `build/html`.

## Соглашения по переводу

В следующей таблице перечислены принятые переводы некоторых терминов и словосочетаний,
используемых в переводе документации для общей согласованности.

Кроме этого используется терминология из [словаря «Веб-стандартов»](https://github.com/web-standards-ru/dictionary).

| Исходный вариант | Принятый перевод |
| - | - |
| Assertion | Утверждение |
| Code coverage | Покрытие кода |
| Constraint | Ограничение |
| Consumer | Потребитель |
| Data-driven tests | Тесты, управляемые данными |
| Data provider | Провайдер данных |
| Docblock | Докблок |
| Fixture | Фикстура |
| Matcher object | Объект сопоставления |
| Mock Objects | Подставные объекты |
| Mock | Подстановка |
| Mocking | Имитация, подстановка |
| Producer | Продюсер |
| Production | Продакшен |
| Spies | Шпионы |
| Stub | Заглушка |
| Stubbed | Подменённый |
| System Under Test (SUT) | Тестируемая система |
| Test case | Тестовый класс |
| Test doubles | Тестовые двойники |
| Test listeneer | Обработчик тестов |
| Test runner | Исполнитель тестов |
| Test suites | Набор тестов |
