# Closures


 В этой статье мы рассмотрим такие темы как область видимости переменной и замыкания. Сначала, мы разберемся в том, что влияет на область видимости переменной, разберемся в механизме создания и работы функций и переменных, рассмотрим что такое лексическое окружение и как реализована работа с переменными, которые находятся не в глобальной области видимости, а внутри функции. В конце, в главе “Модули”, мы разберем один из примеров реализации замыкания на практике.

# обальный объект
 Глобальными называют переменные и функции, которые не находятся внутри какой-то функции. То есть, иными словами, если переменная или функция не находятся внутри конструкции function, то они – «глобальные».
В JavaScript все глобальные переменные и функции являются свойствами специального объекта, который называется «глобальный объект» (global object).
В браузере этот объект явно доступен под именем window. Объект window одновременно является глобальным объектом и содержит ряд свойств и методов для работы с окном браузера.
Присваивая или читая глобальную переменную, мы, фактически, работаем со свойствами window.
 
# Порядок инициализации
Выполнение скрипта происходит в две фазы:
На первой фазе происходит инициализация, подготовка к запуску.
Во время инициализации скрипт сканируется на предмет объявления функций вида function имя(параметры) {...}, а затем – на предмет объявления переменных var. Каждое такое объявление добавляется в window.
Обычные функции вида function имя(параметры) {...}  сразу после инициализации могут быть вызваны, а переменные – равными undefined.
На второй фазе – собственно, выполнение.
Присваивание (=) значений переменных происходит, когда поток выполнения доходит до соответствующей строчки кода, до этого они являются undefined.
Конструкции for, if... не влияют на видимость переменных. На видимость переменных влияют только функции. Фигурные скобки, которые используются в for, while, if, в отличие от объявлений функции, не влияют на видимость переменных.
В JavaScript нет разницы между объявлением вне блока:
   var i;
{
  i = 5;
}
…И внутри него:

     i = 5;
{
  var i;
}
Также нет разницы между объявлением в цикле и вне его:
        for (var i = 0; i < 5; i++) { }
Идентичный по функциональности код:
 var i;
for (i = 0; i < 5; i++) { }
В обоих случаях переменная i будет создана до выполнения цикла, на стадии инициализации кода, т.е. еще до запуска цикла, и ее значение будет сохранено после окончания цикла. 
 
№ Лексическое окружение

Все переменные внутри функции – это свойства специального внутреннего объекта LexicalEnvironment, который создаётся при её запуске. Также, при создании функция получает специальное внутреннее свойство [[Scope]], которое ссылается на лексическое окружение, в котором она была создана.
При запуске функция создает объект LexicalEnvironment, записывает туда аргументы, функции и переменные. Процесс инициализации выполняется в том же порядке, что и для глобального объекта.
Из функции мы можем обратиться не только к локальной переменной, но и к внешней. Интерпретатор, при доступе к переменной, сначала пытается найти ее в текущем LexicalEnvironment, а затем, если её нет – ищет во внешнем LexicalEnvironment. Такой порядок поиска возможен благодаря тому, что ссылка на внешний объект переменных хранится в свойстве [[Scope]].
 Заметим, что если переменная не найдена во внешнем LexicalEnvironment, то она ищется в ещё более внешнем (через [[Scope]] внешней функции). Если переменная не будет найдена нигде, то результатом выполнения функции будет ошибка. 
Значение переменной из внешней области берётся всегда текущее. Стоит отметить, что оно может быть уже не то, что было на момент создания функции.
 
№ Модуль
 
Допустим, мы хотим разработать скрипт, который делает что-то полезное на странице.
Файл hello.js
// глобальная переменная нашего скрипта
var message = "Привет";

// функция для вывода этой переменной
function showMessage() {
  alert( message );
}

// выводим сообщение
showMessage();
У этого скрипта есть свои внутренние переменные и функции.
В данном случае это message и showMessage.
Предположим, что мы хотели бы распространять этот скрипт в виде библиотеки. Каждый, кто хочет, чтобы посетителям выдавалось «Привет» – может просто подключить этот скрипт. Достаточно скачать и подключить, например, как внешний файл hello.js – и готово.
Если подключить подобный скрипт к странице, то возможен конфликт с переменными, которые она использует.
То есть, при подключении к такой странице он её «сломает»:
<script>
  var message = "Пожалуйста, нажмите на кнопку";
</script>
<script src="hello.js"></script>

<button>Кнопка</button>
<script>
  // ожидается сообщение из переменной выше...
  alert( message ); // но на самом деле будет выведено "Привет"
</script>
Автор страницы ожидает, что библиотека "hello.js" просто отработает, без побочных эффектов. А она вместе с этим переопределила message в "Привет".
Если же убрать скрипт hello.js, то страница будет выводить правильное сообщение (“Пожалуйста, нажмите на кнопку”).
Зная внутреннее устройство hello.js нам, конечно, понятно, что проблема возникла потому, что переменная message из скрипта hello.js перезаписала объявленную на странице. 
Чтобы проблемы не было нужно, чтобы у скрипта была своя собственная область видимости, чтобы его переменные не попали на страницу.
Для этого мы завернём всё его содержимое в функцию, которую тут же запустим.
 
(function() {

  // глобальная переменная нашего скрипта
  var message = "Привет";

  // функция для вывода этой переменной
  function showMessage() {
    alert( message );
  }

  // выводим сообщение
  showMessage();

})();
Этот скрипт при подключении к той же странице будет работать корректно.
Будет выводиться «Привет», а затем «Пожалуйста, нажмите на кнопку».
Сегодня мы рассмотрели области видимости, что влияет на область видимости переменной, а также мы рассмотрели замыкания, как они реализованы в JavaScript и преимущества, которые нам дают замыкания. 