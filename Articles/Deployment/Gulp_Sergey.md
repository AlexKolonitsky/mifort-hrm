# Gulp #

Согласно определению данному на официальном ресурсе, Gulp – это потокавая  система  сборки (streaming build system). Она изначально ориентирована на сборку проектов. Однако, вы можете использовать Gulp и для других задач помимо сборки проектов, т.к. как вы можете написать  совершенно разные файлы  gulpconfig.  Именно поэтому  часто можно встретить определение Gulp как системы для описания произвольного вида задач. 

# Преимущества

Сами разработчики выделяют следующие преимущества gulp:
1)	Эффективность – у gulp одна из самых высоких скоростей обработки файлов;
2)	прост в использовании – легко поддерживать уже имеющиеся файлы и писать новые благодаря простому синтаксису;
3)	прост в изучении – достаточно взглянуть на простой пример, чтобы написать свой первый gulpconfig;

# Установка gulp

Пример установки с официального сайта: npm install --global gulp-cli, npm install --save-dev gulp. Gulp нужно ставить и локально (npm install gulp), и глобально (npm install –g gulp). Локальный gulp необходим чтобы использовать и подключать команды require. Глобальный – для написания задач в командной строке. 

# Vinyl fs

Vinyl – объект метаданных, который описывает файл. Это виртуальный объект, в который копируется информация о файле, полученная во время выполнения команды gulp src. В объекте file хранятся следующие свойства : сontents – содержимое файла; Path – путь к файлу; Cwd – директория, в которой запущена команда; base – расположение файла. Сам vinyl только описывает файл, но нам нужен также способ, чтобы получить возможность изменять файлы. Для этого нам  и нужен Vinyl fs. Vinyl fs – это vinyl адаптер для файловой системы. В ядре gulp существует виртуальная файловая система Vinyl fs. Она добавляет src и dest методы для работы с файлами, которые создают поток. По умолчанию, gulp src читает каждый файл и передает в свойство сontents информацию о его содержимом. Src возвращает readable поток объектов типа vinyl. Далее, эти объекты проходят через плагины и в конце попадают в gulp dest. Dest возвращает writable поток и записывает vinyl objects на диск.
Существует ряд полезных опций для gulp src метода:
{read:false} – не читать контент файлов. Используется, когда файлы имеют большой размер;
{since: new Date()} – чтение файлов осуществляется только начиная с указанной даты.
{buffer: false} – создает для каждого файла readable stream.

# Способы сигнализирования о завершении процесса

В gulp все задачи являются асинхронными. Каждая задача должна сигнализировать о том, что она завершена, одним из следующих способов.
1) callback;
2) return new Promise((resolve, rejected) =>{
….resolve("result");
});
return require(‘fs’).createReadStream(__filename);
Важное замечание: возвращенные потоки, промисы и т.д. в gulp будут прочитаны, но результаты не будут переданы дальше, а будут отброшены. Т.е. мы не можем использовать результат выполнения одной задачи в Gulp 


# Плагины gulp

Gulp concat – объединяет несколько файлов в один.
Gulp-if  - если выполняется какое-то условие, указанное в скобках, то выполнится код внутри.
Gulp newer – подключается в цепочку, получает из src файлы в потоке и смотрит: если файл в директории такой же л и у него такая же или более новая дата модификации – он его не пропускает.
Gulp remember – запоминает все файлы, которые через него проходят в своем внутреннем кэше и потом, если в потоке они отсутствуют, то добавляет отсутствующие файлы при следующем вызове.
Gulp autoprefixer – добавляет браузерные префиксы
Gulp source maps (что было и что стало). Когда в него поступает файл, он создает у него свойство sourceMap (file.sourceMap). В дальнейшем большинство плагинов знают про это свойство и если они что-то изменяют в файле, то записывают это в свойство.
Gulp watch - отслеживает файлы и выполняет указанные действия, если файлы были изменены. Предыдущая сборка не полностью удаляется. Новая сборка строится на основе предыдущей.

Browser-sync   (browserSync = require(‘browser-sync’).create(); ) - синхронизирует не только изменения но и действия в браузерах.


# Обработка ошибок

При запуске команды gulp src объект vinyl пропускается через цепочку плагинов. Если в каком-то потоке происходит ошибка, генерируется событие error. Поймать его можно через подключение к событию error  .on(‘error’, function(err) {console.log(err.message)
this.end}). В отличии от node js при возникновении ошибок процесс не умирает. Это происходит благодаря библиотеке async done, которая запускает задачи и ждет их выполнения. Для того чтобы понять что именно пошло не так, используются следующие плагины:
1)	gulp notify – подключается к потоку и отслеживает событие onerror. В случае его возникновения выводит в консоль информацию об ошибке;
2)	Gulp plumber – возвращает специальный поток, который ничего не делает с данными. В него встроен обработчик ошибок, который не только подпишется на error для одного плагина, но и автоматически сделает это и для всех последующих плагинов, подключенных через pipe;
3)	Gulp multipipe – объединяет несколько потоков в один. Когда мы берем единый поток и в него что-то пайпим, то он передает данные по цепочке.

# Сравнение популярных систем сборки


	grunt	Gulp	gear	brunch
начало работы	 
Порог вхождения чрезвычайно низок. Что касается порога вхождения в уже готовые проекты, то тут дела обстоят немного иначе.	 
Начало работы с Gulp такое же быстрое, как и с Grunt. Однако, оно лишено изъянов, которые мы обсуждали в начале работы с grunt для больших проектов, т.е. Gulp так же легко читать, как и писать, что делает его максимально располагающим к быстрому старту. 	 
Высокий порог вхождения: в документации нет понятных примеров.	 
Все достаточно понятно.
производительность	Не использует многопоточность, не использует кеширование.
 
~+180ms
Использует многопоточность, использует всего один поток ввода/вывода при выполнении ряда задач с файлами.
~+30ms	-//-
300ms	-//-
~+80ms
документация	Документация компактная и полностью удовлетворяющая любым запросам. Всё необходимое для работы с системой можно найти на оф. сайте.	Документация немого уступает grunt, однако в целом все понятно.	Документация этого проекта — простой набор API, мол, «что хочешь — то и делай». Никаких guide line или best practice вы здесь не найдете.	В git лежит 5 файлов, которые подробно описывают работу с системой и 1 FAQ. Информация простая, легкая к восприятию, но всё же хотелось бы чего-то большего. 

плагины	 2000+ плагинов, однако некоторая часть не обновлялась после релиза и/или не выпустила ни одного релиза. 	200+ плагинов, и количество стремительно увеличивается.	Кроме gear-lib с 5 плагинами, для этого сборщика ничего не существует.	50+ плагинов, тенденция к очень медленному увеличению количества.
