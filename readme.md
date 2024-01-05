# Типы данных

Скалярные типы:

* bool
* int
* float (иногда встречается название double что для php не имеет разницы)
* string

Смешанные типы:

* array
* object
* callable
* iterable = array | Traversable специальный тип для отделения массивов от иттераторов

```
  iterable ---> array
           --> Traversable ---> Iterator
                           --> IteratorAggregate
                           --> Generator
```

Специальные типы:

* resource
* null

# Работа с ссылками

* Передача по ссылке - передача параметров по ссылке. При этом локальная переменная в функции и переменная в вызывающей области видимости ссылаются на одно и то же содержимое.
* Присвоение по ссылке - ссылки PHP позволяют создать две переменные указывающие на одно и то же значение.
* Возврат по ссылке - используется в тех случаях, когда вы хотите использовать функцию для выбора переменной, с которой должна быть связана данная ссылка.
* Неявное использование механизма ссылок

Одним из ключевых моментов объектно-ориентированной парадигмы PHP, который часто обсуждается, является "передача объектов по ссылке по умолчанию". Это не совсем верно. Данный раздел уточняет это понятие, используя некоторые примеры.

Ссылка в PHP - это псевдоним (алиас), который позволяет присвоить двум переменным одинаковое значение. В PHP объектная переменная больше не содержит сам объект как значение. Такая переменная содержит только идентификатор объекта, который
позволяет найти конкретный объект при обращении к нему. Когда объект передаётся как аргумент функции, возвращается или присваивается другой переменной, то эти разные переменные не являются псевдонимами (алиасами): они содержат копию
идентификатора, который указывает на один и тот же объект.

# Copy-on-write

Каждый раз передавая переменную в функцию её значение (теоретически) должно быть скопировано. Это может не быть проблемой для данных типа integer или float, но представьте, что вы передаете в функцию массив, содержащий десять миллионов
значений. Для того чтобы избежать этого в PHP используется парадигма копирования-при-записи (copy-on-write). Так, на самом деле, при передаче массива его копирование не происходит до того момента, пока вы не захотите изменить что либо в
этом массиве

# Run time error handling

В 7-й версии php произошли ряд изменений которые касаются иерархии ошибок (больше информации по иерархии), в остальном ожидается что соискатель будет знать как отлавливать исключения, или даже сможет написать свой собственный error_handler.

# Замыкания

Они же анонимные функции, наиболее полезны в качестве значений callback-параметров

```php
<?php
$message = 'привет';

// Без "use"
$example = function () {
    var_dump($message);  Notice: Undefined variable: message in /example.php on line 6
};
$example();

// Наследуем $message
$example = function () use ($message) {
    var_dump($message); NULL
};
$example();

// Значение унаследованной переменной задано там, где функция определена,
// но не там, где вызвана
$message = 'мир';
$example(); string(12) "привет"

// Сбросим message
$message = 'привет';

// Наследование по ссылке
$example = function () use (&$message) {
    var_dump($message); string(12) "привет"
};
$example(); string(12) "привет"

// Изменённое в родительской области видимости значение
// остаётся тем же внутри вызова функции
$message = 'мир';
echo $example(); string(6) "мир"

// Замыкания могут принимать обычные аргументы
$example = function ($arg) use ($message) {
    var_dump($arg . ', ' . $message);
};
$example("привет"); string(20) "привет, мир"

// Объявление типа возвращаемого значения идет после конструкции use
$example = function () use ($message): string {
    return "привет, $message";
};
var_dump($example()); string(20) "привет, мир"
?>
```

# Функции по работе с массивами

array_intersect — Вычисляет пересечение массивов (обратное от array_diff)

# Функция list()

Присваивает переменным из списка значения подобно массиву

```php
$info = array('кофе', 'коричневый', 'кофеин');
list($drink, $color, $power) = $info;
```

# Область видимости переменной

В большинстве случаев, все переменные имеют только одну область видимости, исключение составляют глобальные/суперглобальные переменные которые, как ясно из названия, доступны везде, а также статические/константы.

# Область видимости свойства/метода/константы класса

Определяется при помощи ключевых слов public (видно всем), protected (доступен только текущему классу или его наследникам) или private (доступен только текущему классу),

# Final классы и методы

Разместив ключевое слово final перед объявлениями методов класса, можно предотвратить их переопределение в дочерних классах, а в случае объявления самого класса как final — его невозможно будет унаследовать.

# Позднее статическое связывание

Выражается в разнице между вызовом self:: и static::. «Позднее связывание» означает что static:: будет вычисляться в ходе исполнения и может быть переопределен в классах — наследниках.

Статические ссылки на текущий класс, такие как self:: или __CLASS__, вычисляются используя класс, к которому эта функция принадлежит, как и в том месте, где она была определена:

::static - видит то откуда вызвали

# Магические методы

__construct(), __destruct(), __call(), __callStatic(), __get(), __set(), __isset(), __unset(), __sleep(), __wakeup(), __serialize(), __unserialize(), __toString(), __invoke(), __set_state(), __clone() и __debugInfo()

Все магические методы, за исключением __construct(), __destruct() и __clone(), ДОЛЖНЫ быть объявлены как public, в противном случае будет вызвана ошибка уровня E_WARNING. До PHP 8.0.0 для магических методов __sleep(), __wakeup(), __
serialize(), __unserialize() и __set_state() не выполнялась проверка.

Однако __construct() и __destruct() не должны объявлять возвращаемый тип; в противном случае выдаётся фатальная ошибка.

## __sleep() и __wakeup()

Функция serialize() проверяет, присутствует ли в классе метод с магическим именем __sleep(). Если это так, то этот метод выполняется до любой операции сериализации. Он может очистить объект и должен возвращать массив с именами всех
переменных этого объекта, которые должны быть сериализованы. Если метод ничего не возвращает, то сериализуется null и выдаётся предупреждение E_NOTICE.

## __serialize() и __unserialize()

serialize() проверяет, есть ли в классе функция с магическим именем __serialize(). Если да, функция выполняется перед любой сериализацией. Она должна создать и вернуть ассоциативный массив пар ключ/значение, которые представляют
сериализованную форму объекта. Если массив не возвращён, будет выдано TypeError.

## __invoke()

вызывается, когда скрипт пытается выполнить объект как функцию.

## __debugInfo()

Этот метод вызывается функцией var_dump()

# Итераторы

PHP предоставляет такой способ объявления объектов, который даёт возможность пройти по списку элементов данного объекта, например, с помощью оператора foreach. По умолчанию, в этом обходе (итерации) будут участвовать все видимые свойства
объекта.

Например перебор большого csv файла

# Генераторы (yield)

Вся суть генератора заключается в ключевом слове yield. В самом простом варианте оператор "yield" можно рассматривать как оператор "return", за исключением того, что вместо прекращения работы функции, "yield" только приостанавливает её
выполнение и возвращает текущее значение, и при следующем вызове функции она возобновит выполнение с места, на котором прервалась.

# Reflection API

Позволяет получить данные о классе

* ReflectionClass: сообщает информацию о классе.
* ReflectionFunction: сообщает информацию о функции.
* ReflectionParameter: извлекает информацию о параметрах функции или метода.
* ReflectionClassConstant: сообщает информацию о константе класса.

# Autoloader

```php
spl_autoload_register(function ($class) {
    // Получаем путь к файлу из имени класса
    $path = __DIR__ . $class . '.php';
    // Если в текущей папке есть такой файл, то выполняем код из него
    if (file_exists($path)) {
        require_once $path;
    }
    // Если файла нет, то ничего не делаем - может быть, класс 
    // загрузит какой-то другой автозагрузчик или может быть, 
    // такого класса нет
});
```

# SPL (Standard PHP Library)

набор классов (структуры данных, итераторы, исключения, классы для обработки файлов), интерфейсов и функций для решения стандартных задач.

https://www.php.net/manual/ru/book.spl.php

# APC/APCu

Alternative PHP cache (APC) или альтернативный кеш без опкода (APCu) — бесплатный кеш с открытым исходным кодом, предназначенный для кэширования и оптимизации промежуточного кода PHP. В основном используется для сохранения вычислений между
запросами.

# OPcache

Улучшает производительность PHP путём сохранения скомпилированного байт-кода скриптов в разделяемой памяти, тем самым избавляя PHP от необходимости загружать и анализировать скрипты при каждом запросе.

# Rest API

REpresentational State Transfer — архитектурный подход который позволяет организовать взаимодействие между приложениями используя все возможности HTTP
В контексте API нужно понимать основные форматы обмена данными, чаще всего это XML и JSON.

# OOP

## Инкапсуляция

Это механизм, который объединяет данные и методы, манипулирующие этими данными, и защищает их от внешнего вмешательства или неправильного использования. Когда методы и данные объединяются таким способом, создается объект. Т.е.
переменные состояния объекта скрыты от внешнего мира. Изменение состояния объекта (его переменных) возможно ТОЛЬКО с помощью его собственных методов. Можно сказать, что инкапсуляция подразумевает под собой сокрытие данных, что позволяет эти
данные защитить.

## Наследование

Это процесс, посредством которого, один объект может наследовать свойства другого объекта и добавлять к ним черты, характерные только для него.

## Полиморфизм

Это свойство, которое позволяет одно и тоже имя использовать для решения нескольких технически разных задач. Проще говоря, концепцией полиморфизма является идея “один интерфейс, множество реализаций”. Это означает, что можно
создать общий интерфейс для группы близких по смыслу действий.

# Абстрактные классы/методы

* Не предполагает создание экземпляров.
* Реализуют на практике один из принципов ООП — полиморфизм.
* Может содержать абстрактные методы и свойства. Абстрактный метод не реализуется для класса, в котором описан, однако должен быть реализован для его не абстрактных потомков.
* Представляют собой наиболее общие абстракции, то есть имеющие наибольший объем и наименьшее содержание.

# Интерфейсы

Интерфейс, в отличие от абстрактного класса, не может содержать поля и методы, имеющие реализацию – он описывает только сигнатуры метода, которые должны реализовать его наследники. В отличие от того же абстрактного класса, класс может
реализовывать одновременно множество интерфейсов.

# Traits

Трейт очень похож на класс, но предназначен для группирования функционала и его дальнейшего переиспользования во множестве классов, которые не могут быть связаны одним абстрактным классом. Иногда еще говорят, что трейт — это способ
реализовать множественное наследование в PHP.

# GRASP шаблоны

General Responsibility Assignment Software Patterns — шаблоны проектирования для решения общих задач.

# Паттерны банды четырех

[Паттерны](pattern.md)

# SOLID

S: Single Responsibility Principle (Принцип единственной ответственности).
O: Open-Closed Principle (Принцип открытости-закрытости).
L: Liskov Substitution Principle (Принцип подстановки Барбары Лисков).
I: Interface Segregation Principle (Принцип разделения интерфейса).
D: Dependency Inversion Principle (Принцип инверсии зависимостей).

# Kiss

Keep it simple, stupid — хороший принцип, вся суть которого уже содержится в названии,

# Dry

Don’t repeat yourself — принцип, описывающий что необходимо переиспользовать код везде, где это возможно.

# Авторизация и аутентификация

Авторизация — предоставление прав на выполнение определённых действий, а также процесс проверки прав при попытке выполнения этих действий.
Аутентификация — процедура проверки подлинности, например проверка подлинности пользователя путем сравнения введённого им пароля.
