# Type Script


В этой статье я попытаюсь раскрыть все особенности языка TypeScript для комфортной разработки приложений на Angular 2.

Перед описанием основных особенностей стоит заметить, что Ts поддерживает большинство фишек Es6 (let, import, arrow function etc.).

Статическая типизация: 
Является одним их ключевых отличий  Ts от Js. Если в Js мы никак для переменной тип не указываем, то в Ts типы указывать можно, и даже нужно. Если же тип не указан, то переменной будет иметь тип any. Кстати про типы в Ts, их 9:
Boolean
Number
String
Array
Turple (кортежи)
Enum
Any (произвольный тип)
null,undefined
Void (отсутствие конкретного типа)


Так вот, переменные в Ts создаются следующим образом:
	let x: boolean = true;


Если же значение переменной не соответствует типу, то в процессе компиляции компилятор выдаст ошибку и приложение мы запустить не сможем. 


Статическая типизация помогает в случаях, когда мы хотим, чтобы функция работала с одним типом данных. К примеру, приведенная ниже функция, в зависимости от входных данных, может выполнять разные действия: складывать числа, склеивать строки и т.д. И до конца непонятно, что же всё таки происходит, пока мы не запустил программу.



function sum(a,b) {
	return a+b;
}

TypeScript решает данную проблему: всего лишь нужно указать тип у входных параметров функции.



Классы:


В Ts реализован объектно-ориентированный подход.  Для объявления класса используется ключевое слово class. В описании класса есть поля и методы класса.


class Person {
    name: string;     // поле класса
    lastname: string;
    getFullName(): string {   // метод класса
        return this.name + "  " + this.lastname;
    }
}

В классе помимо методов есть еще и конструкторы. Конструкторы - это специальные функции, которые выполняют начальную инициализацию объекта. В TypeScript имеется возможность определять несколько версий конструктора для указания разного количества и типа аргументов.

Классы наследуются с помощью ключевого слова extends.


Интерфейсы:


Интерфейс определяет свойства и методы, которые должен реализовывать объект. Определяется интерфейс ключевым словом interface следующим образом:
interface IPerson {
     name: string;
    age: number;
}


Создадим объект , реализующий данный интерфейс:
let employee: IPerson = {
    name: "Bob”,
    age: 30
}
Данная реализация накладывает некоторые ограничения на employee: он должен реализовать все те свойства и методы интерфейса IPerson, т.е. в нашем случае должен включать в себя свойства name, age.

Интерфейсы в Ts ,помимо объектов, могут быть применены ко всем базовым конструкциям( классы, массивы, функции и т.д.).

Считаю, что Ts больше подходит для больших проектов, чем Js. Связано это с несколькими особенностями TypeScript:
Статическая типизация (уменьшает количество потенциальных ошибок, которые могли бы возникнуть при разработке на JavaScript).
В TypeScript реализованы все те концепции, которые свойственны объектно-ориентированным языкам.
TypeScript является надмножеством JavaScript, а это значит, что любая программа на JS является программой на TypeScript. Более того код на TS компилируется в JS, который поддерживается абсолютным большинством браузеров (т.к. ориентируется на стандарт EcmaScript 3, но поддерживает и другие стандарты языка)










