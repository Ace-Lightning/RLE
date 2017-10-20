[![Build status](https://ci.appveyor.com/api/projects/status/kc45ibad8ce19c3q?svg=true)](https://ci.appveyor.com/project/Ace-Lightning/rle)

# RLE packer/unpacker
Программа запаковывает или распаковывает данные для игры "Double Dribble", которая вышла в 1987 году на платформе NES.

## Описание формата RLE
Первый байт запакованной последовательности - это ключевой байт. Если он меньше или равен 0x80, то следующий байт запакованной последовательности будет повторяться равное ключевому байту количество раз. 

> **14** *05* -> *05 05 05 05 05 05 05 05 05 05 05 05 05 05* 

Если ключевой байт больше 0x80, то следующее количество байт запакованной последовательности, равное ключевому байту за вычетом 0x80 будет скопировано как есть. 

> **85** *17 05 FF 07 07* -> *17 05 FF 07 07*

Запакованная последовательность должна заканчиваться байтом 0xFF. 

Ключевой байт может принимать значения до 255. Это накладывает некоторые ограничения, поэтому максимальное количество одинаковых, подряд идущих, байт, которое может быть запаковано одним ключевым байтом равно 0x80, а максимальное количество байт, которое не образует последовательность одинаковых значений, равно 0x7E, поскольку значение 0xFF ключевого байта обрабатывается распаковщиком как стоп-байт. 

Так же стоит обратить внимание, что последовательность одинаковых симовлов длиной два байта запаковывать не имеет смысла, поскольку длина запакованной последовательности будет равна так же двум байтам. Имеет смысл запаковывать последовательность в которой три и больше байт.

## Работа с программой
Работа с программой происходит в консоли. Программа может принимать четыре параметра

> режим\_работы путь\_к\_открываемому\_файлу путь\_к\_сохраняемому\_файлу начальная\_позиция

- **режим\_работы** - может принимать два значения
	- *p (от слова pack)* - включает режим запаковки
	- *u (от слова unpack)* - включает режим распаковки
- **путь\_к\_открываемому\_файлу** - путь к отрываемому файлу, содержащему либо запакованную, либо незапакованную последовательность. Обратите внимание на то, что если последовательность незапакована, программа будет обрабатывать файл от заданной начальной позиции, до его конца. Распаковка ведется от заданной позиции, до того момента, пока ключевой байт не станет равен 0xFF
- **путь\_к\_сохраняемому\_файлу** - путь к сохраняемому файлу. Обратите внимание, что если файл с таким именем уже существует, то программа его перезапишет
- **начальная\_позиция** - начальная позиция в открываемом файле. Этот параметр не является обязательным, если он не указан, то чтение файла начинается с его начала. Начальная позиция может быть указана как в десятичном виде, так и в шестнадцатиричном виде, с помощью префикса '0x'.

*Если в вашем пути содержатся пробелы, то заключайте путь в двойные кавычки.*

Пример работы с программой:
> RLE.exe "C:\My Files\fileOpen.nes" C:\fileSave.nes 0x3FC
