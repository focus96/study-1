# Composer


## Базовые настройки

Выполняем `composer init`, и ставим:
 - name &mdash; `yourname/composer` (у меня вышло dmitrybubyakin/composer)
 - description &mdash; `Lesson 1: work with composer`

Все остальные поля - по умолчанию. 

P.S. Дальше, везде где видите `DmitryBubyakin\ClassName` меняем на свой namespace.

---

Для работы нам потребуется phpunit, единственный раз напишу, любой пакет через composer добавляется таким образом:

`composer require username/package`

Можно так же добавить ключ `--dev` (`require --dev`), разница в том, 
что зависимости добавленные в блок `require-dev` будут доступны только в development режиме (подумайте над этим, прежде чем что-либо добавлять).

Вот [ссылка](https://packagist.org/packages/phpunit/phpunit) на phpunit, ставим.

---

Создадим папку `src` и в ней класс `Application` (`src/Application.php`):

```php
<?php

namespace DmitryBubyakin;

class Application 
{

    /**
     * Добавим к переданному сообщению '!!!'.
     * 
     * @param  string $message Сообщение
     * @return string
     */
    public function saySomething($message) 
    {
        return $message . '!!!';
    }
}
```

И файл `public/index.php`

```php
<?php
// указываем путь к файлу autoload, в котором подключаются все зависимости добавленные в composer.json
// если забудем, то наш класс DmitryBubyakin\Application будет недоступен тут.
// если кому интересно, откройте этот файл и попробуйте разобраться что да как
require __DIR__.'/../vendor/autoload.php';

echo 'Hello<br>';

try {
    // у нас полетит исключение, так как файл на найден, мы нигде не указали, что он существует
    $app = new DmitryBubyakin\Application;
    echo $app->saySomething('Lesson 1: work with composer.');

    // читаем
    // http://php.net/manual/ru/language.errors.php7.php
    // https://habrahabr.ru/post/261451/
} catch (Throwable $e) {
    // выведем сообщение ошибки
    echo $e->getMessage();
}

```

Если Вы еще не любите консоль, то обязаны полюбить. В папке public выполняем команду:

`php -S localhost:8000`

И теперь наш [сайт](http://localhost:8000) будет доступен (если у Вас не забит 8000 порт, если так, ставим любой другой, например 8213)

Если у Вас не отображается подобный контент:

```
Hello
Class 'DmitryBubyakin\Application' not found
```

Значит Вы что-то сделали не так, разбирайтесь.

composer.json ничего не знает о нашем классе, поэтому, добавим такую вот информацию:

```json
    "autoload": {
        "psr-4": {
            "DmitryBubyakin\\": "src/"
        }
    }
```

Теперь, если namespace начинается с `DmitryBubyakin`, то файлы будут цепляться из папки src.

И у нас должно отображаться что-то подобное:

```
Hello
Lesson 1: work with composer!!!
```

## PHPUnit

Теперь приступим к настройке.

создадим файл `phpunit.xml` и мельком пробежим глазами [тут](https://phpunit.de/manual/current/en/appendixes.configuration.html)

Содержимое `phpunit.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit backupGlobals="false"
         backupStaticAttributes="false"
         bootstrap="vendor/autoload.php"
         colors="true"
         convertErrorsToExceptions="true"
         convertNoticesToExceptions="true"
         convertWarningsToExceptions="true"
         processIsolation="false"
         stopOnFailure="false">
    <testsuites>
        <testsuite name="Application Test Suite">
            <directory suffix="Test.php">./tests</directory>
        </testsuite>
    </testsuites>
    <php>
        <env name="APP_ENV" value="testing"/>
    </php>
</phpunit>
```
 - bootstrap &mdash; указываем на файл, с которого стартует наше приложение (как мы делали в index.php)
 - `<directory></directory>` &mdash; тут мы указываем где будут лежать наши тесты
 - `<php></php>` &mdash; здесь мы можем настроить переменные среды

Все остальное пока что маловажно, разберетесь сами.

Создадим файл `tests/ExampleTest.php`:

```php
<?php

use PHPUnit\Framework\TestCase;
use DmitryBubyakin\Application;

class ExceptionTest extends TestCase
{

    /**
     * Проверяем наличае метода sayHello и его работоспособность.
     */
    public function testSayHello()
    {
        $app = new Application;

        // проверяем наличае метода
        $this->assertTrue(method_exists($app, 'saySomething'));
        // проверяем, что нет такого метода
        $this->assertFalse(method_exists($app, 'undefinedMethod'));

        // проверяем работу метода
        $message = 'test';
        $expect = 'test!!!';

        $this->assertEquals($app->saySomething($message), $expect);
    }
}
```

Классы должны иметь суфикс `Test`. Методы должны начинать с `test`.
Тестировать все в одном классе и в одном методе нельзя. Структурируйте тесты по мере разростания приложения. Один тест должен отвечать за что-то одно.



И выполняем команду `./vendor/bin/phpunit`, будет примерно такой вывод:

```
PHPUnit 6.1.4 by Sebastian Bergmann and contributors.

.                                                                   1 / 1 (100%)

Time: 33 ms, Memory: 4.00MB

OK (1 test, 3 assertions)

```

В итоге у нас должна быть примерно такая структура файлов:

```
composer
    public
        index.php
    src
        Application.php
    tests
        ExampleTest.php
    vendor
    composer.json
    phpunit.xml
```
## Задание

[Вот](https://phpunit.de/manual/current/en/appendixes.assertions.html) список всех assert'ов, которые поддерживает PHPUnit.

Написать несколько методов в классе Application и тесты для них.

Потренеруйтесь с массивами, объектами и т.п.

Затем все запушить к себе в репозиторий.