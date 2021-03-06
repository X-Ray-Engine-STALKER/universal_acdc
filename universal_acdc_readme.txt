----------------------------------------------------
-----S.T.A.L.K.E.R. *.spawn compiler/decompiler-----
----------------------------------------------------

Версия: 1.38
Дата последних правок: 21.05.2014

Кратко: данный acdc предназначен для распаковки и запаковки all.spawn/level.spawn от 
любого билда, начиная с 1265. 

Основные режимы работы:
	-распаковка оригинальных спавнов ТЧ, ЧН, ЗП и билдов ТЧ, начиная с xrCore build 1265.
	-распаковка спавнов от модов (требуется папка конфигов от мода)
	-конвертация спавна в любую другую версию.
	-массовая замена гейм-вертексов в распакованном спавне.
	-разбивка all.spawn на level.spawn'ы и level.game'ы
	-сравнение распакованных спавнов
	-обновление вертексов согласно координат объектов

Что нужно для работы:
	-собственно, сам спавн (all.spawn, level.spawn)
	-game.graph (только если распаковываете all.spawn). 
Если all.spawn от билда 3120, ЧН или ЗП - game.graph не нужен (он вшит в спавн).
	-папка config/configs (если распаковываете мод и universal_acdc жалуется на unknown section)

Для корректной работы используйте с папкой stkutils строго последней версии (stkutils_07_08_2013).

Использование:
Кладете all.spawn (или level.spawn) и game.graph в папку с программой, делаете батник (команды ниже),
запускаете батник, радуетесь жизни.

Пояснение по дальнейшему тексту: в угловых скобочках (<>) указано значение опции. Это путь до спавна,
папка для распаковки и т.п., то есть то, что нужно вписывать вам. В квадратных скобочках ([])
указаны необязательные опции. Опции без скобочек - обязательны.

----------------------------------------------------
----------------[Распаковка спавна]-----------------
----------------------------------------------------
Режим предназначен для распаковки файлов all.spawn и level.spawn

Команда:  universal_acdc.pl -d <spawn_file> [common_options]

-d <spawn_file> - путь до спавна.
common_options - общие опции. Читайте про них ниже.

При распаковке спавнов от модов может возникнуть ошибка "unknown section".
Это означает, что в моде используются нестандартные секции предметов/мобов.
Для распаковки такого спавна необходимо использовать ключ -scan, указав ему в качестве параметра
путь до папки config/configs. Пример смотрите в батнике acdc_decompile_scan.

----------------------------------------------------
----------------[Запаковка спавна]------------------
----------------------------------------------------
Режим предназначен для запаковки распакованных текстовых файлов спавна в all.spawn или level.spawn

Команда:  universal_acdc.pl -compile <dir> [-idx <index_file>] [-f <flag1,flag2,...>] [common_options]

-compile  <dir> - папка, в которой лежит распакованный спавн. Если работаете в текущей папке, <dir> не нужно.
common_options - общие опции. Читайте про них ниже.
-idx <index_file> - с этим ключом скрипт сформирует ltx конфиг с секциями вида:

[13_box_wood_01_0021]		;формат - "индекс_локации"_"имя_объекта"
id = 2907			;id объекта
story_id = -1			;story_id объекта

Такие записи создадутся для ВСЕХ объектов из спавна.
Если указать ключ -idx без пути до конфига, он появится в папке с universal_acdc (spawn_ids).
Зачем это нужно? В игре вы сможете открыть этот файл из скриптов и найти нужный вам объект.
Если раньше для нахождения нужного id объекта по имени требовалось перебрать все объеты в игре,
теперь это можно сделать, просто считав id из нужной секции конфига.

Также есть кое-какие дополнительные фичи:
- при компиляции для правильной работы необязательно проставлять всем секциям в спавне
параметры version и script_version. Достаточно поставить их в секции актора, ацдц далее будет использовать их.
Полезно, когда спавн "сборный".
- при компиляции контролируется уникальность параметров story_id, чтобы не мучаться потом с вылетами игры.

----------------------------------------------------
------------[Конвертирование спавна]----------------
----------------------------------------------------
Режим позволяет конвертировать спавны ТЧ, ЧН, ЗП между собой.

Команда:  universal_acdc.pl -convert <file> -version <new_version> [-ini <file>] [common_options]

-convert <file> - файл, который нужно сконвертировать. Можно указывать как запакованный (*.spawn) спавн, 
так и распакованный (alife_***.ltx). Внимание! Так как граф и кросс-таблицы в ЧН и ЗП вшиты в спавн,
для конвертирования ЗАПАКОВАННЫХ спавнов ТЧ в эти форматы вам потребуется положить в папку с universal_acdc 
папку levels из игры. Необязательно копировать все файлы, достаточно оставить в папке с каждой локацией 
файл level.gct.

-version <new_version> - новая версия спавна. Подобрать версию вы сможете, посмотрев файл spawn_versions.txt

-ini - файл тонкой настройки конвертации (convert.ini). Если не указан, используется convert.ini, лежащий 
в текущей папке.
common_options - общие опции. Читайте про них ниже.

Также поддерживается тонкая настройка конвертации через файл convert.ini. 
В файле есть две секции: [exclude] и [change]. В секции exclude можно прописать те секции,
которые надо удалить из спава при конвертации. Пример:

[exclude]
sections = m_trader, m_car, flesh_weak

Также поддерживается маскирование. Вместо кучи однотипных секций (stalker_zombied, stalker_sakharov и т.д.)
можно задать маску, используя символ *. Пример: stalker*. Такая запись приведет к исключению всех 
секций, в названии которых есть слово stalker.

В секции change прописываются имена тех секций, в которых нужно что-то поменять или дополнить. 
Пример:

[change]
sections = inventory_box

Далее заполняете файл записями для тех секций, которые вы прописали в change. Пример:

[inventory_box]			//section_name нужной секции
add:custom_data = PREVED	//префикс add используется для тех параметров, в которые нужно добавить
add:game_vertex_id = 10000	//необходимое значение (если число - складывается, если строка - добавляется в конец)
rep:level_vertex_id = 0	//префикс rep используется для параметров, которые необходимо заменить на что-то

Общий пример. Следующий конфиг приведет к тому, что у всех сталкеров прибавится 500 к гейм-вертексу и 
визуал сменится на визуал вояки в противогазе:

[exclude]

[change]
sections = stalker

[stalker]	
add:game_vertex_id = 500
rep:visual_name = actors/soldier/soldier_antigas.ogf

----------------------------------------------------
----------[Массовая замена вертексов]---------------
----------------------------------------------------
При подключении новых локаций без перекомпиляции графа возникает необходимость одновременного
сдвига всех гейм-вертексов спавна новых локаций на какое-то определенное значение.
Это можно сделать в этом режиме.

Команда:  universal_acdc -parse <file> -old <old_gvid0> -new <new_gvid0> [-way] [common_options]

-parse <file> - имя ltx, в котором находится спавн.
-old <old_gvid0> - старый начальный game_vertex_id локации.
-new <new_gvid0> - новый начальный game_vertex_id локации.
-way - обрабатывает аткже файл way_***.ltx для этой же локации.

Пример: universal_acdc -parse alife_l01_escape.ltx -old 0 -new 934

----------------------------------------------------
-------[Разбивка all.spawn на level.spawn]----------
----------------------------------------------------
Разбивка all.spawn на level.spawn может быть полезна при одновременой правке
спавна в сдк и с помощью ацдц.

Команда:  universal_acdc -split <file> [-use_graph] [-way] [common_options]

-split <file> - разбиваемый спавн. Для восстановления граф-поинтов необходимы level.spawn для всех локаций,
находящихся в графе. Их следует поместить в папку levels согласно их положению в геймдате сталкера - по папкам,
соответствующим локациям. Папка levels должна быть в папке с universal_acdc (или путь до нее можно задать ключом - читайте далее).
-use_graph - использование game.graph для восстановления граф-поинтов. Плюс - не нужны level.spawn, минус - 
не восстанавливаются имена граф-поинтов (за исключением граф-поинтов переходов).
-way - также из спавна генерируются level.game

----------------------------------------------------
-----[Сравнение файлов распакованного спавна]-------
----------------------------------------------------
В этом режиме сравниваются два текстовых файла спавна. Итоговый файл формируется на основе первого файла. 
Все секции, которых нет во втором файле, но есть в первом, удаляются, а те секции, которые есть во втором, 
но нет в первом, переносятся в итоговый файл. Параметры секций не меняются. Режим позволяет
сэкономить время в случае, если надо совместить два файла, причем секции там идут вразнобой.
 
Команда:  universal_acdc -compare <file1,file2> [common_options]

-compare <file1,file2> - файлы для сравнения

----------------------------------------------------
-------[Обновление вертексов по координатам]--------
----------------------------------------------------
Зачем нужно: при изменении аи-сетки у объектов изменяются game_vertex_id и level_vertex_id.
Это влечет за собой необходимость повторного снятия данных параметров в игре. Данный режим
позволяет автоматически обновить вертексы у всех секций спавна.

Команда: universal_acdc -update <spawn_name> [common_options]

-update <spawn_name> - all.spawn, для которого надо обновить вертексы.

Режим update требует дополнительной настройки. В папке с acdc есть файл fs_vertex.ltx
В этот файл необходимо вписать путь до локаций с ОБНОВЛЕННОЙ аи-сеткой. Обязательно
должны присутствовать файлы level.ai, level.gct для всех локаций из спавна.
ВНИМАНИЕ! Для нормальной отработки режима геймграф должен быть СТАРЫЙ, использовавшийся
до перекомпиляции. После нормальной отработки режима спавн можно распаковывать уже с НОВЫМ
геймграфом.

----------------------------------------------------
------------------[Общие опции]---------------------
----------------------------------------------------

-out <file> - путь до файла/папки с результатом. Имеет разный смысл для разных опций:
	для decompile, parse - папка с результатом
	для compile, convert - результрующий файл.
	для split - папка levels, куда сохранять level.spawn.

-scan <scan_dir> - путь до папки с конфигами. Используется в случае спавнов от модов.
-g <graph_dir> - путь до папки с game.graph. Бесполезен для compile и parse, а также
если работаете со спавном ЧН, ЗП, build 3120.
-level - обрабатывать спавн как level.spawn. 
-af - также распаковываются\запаковываются места спавна артефактов в аномалиях (section2.bin).
-nofatal - отключает вылет при фатальной ошибке, заменяя ее предупреждением.
-sort <type> - включает сортировку alife-объектов. Имеет два состояния: simple - сортировка по name по алфавиту,
complex - сортировка сначала по section_name, потом по name по алфавиту.

======================================

Если universal_acdc генерирует ошибку 'unknown clsid ... for section...', это означает,
что в данном моде добавлены новые пары клиентский/серверный класс в class_registrator.script.
Дабы не усложнять работу с universal_acdc, такие пары не определяются автоматически.
Лучше всего сообщить об этом мне (http://www.amk-team.ru/forum/index.php?showuser=11696),
однако, если вы понимаете, что находится внутри class_registrator.script, можете добавить
новые сеты в clsids.ini сами. Сеты добавляются в таком формате:

clsid = соответствующий_серверный_класс

Пример:
ZS_ELECT  = se_zone_anom

======================================

Любые имена опций можно сокращать. К примеру, необязательно использовать именно -use_graph, скрипт поймет также и -use, и -u.
Однако ряд опций при сокращении их имени могут конфликтовать друг с другом. -compile нельзя сократить до -c, поскольку
в таком случае скрипту неясно, что имеется ввиду: -compile или -convert. В таком случае наиболее короткое имя для compile
будет таким: -com

======================================

ВАЖНО! При работе со спавнами билдов 25xx перед каждой новой распаковкой ОБЯЗАТЕЛЬНО следует удалять
sections.ini и сканировать конфиги заново.

======================================
История правок:
1.38:
	[!] добавлена поддержка релизного спавна Lost Alpha
	[!] исправлена конвертация и конвертация
1.37:
	[+] добавлена распаковка и запаковка спавна билда Lost Alpha
	[!] все нераспознаные пути при сплите теперь сваливаются в unrecognized_ways.game
1.36:
	[!] исправлена разбивка спавна на level.spawn и level.game
	[!] исправлено сканирование конфигов
1.35:
	[!] исправлено автоназначение версии при отсутствии этого параметра в секции
	[!] убрано чтение game.graph при компиляции
1.34:
	[!] исправлено обновление вертексов
	[+] добавлено обновление параметра distance при обновлении вертексов
	[+] добавлен контроль уникальности story_id при компиляции
1.33:
	[!] исправлена распаковка спавнов нектороых билдов
	[+] добавлен режим обновления вертексов
	[+] добавлен "умный" разбор way-объектов по локациям для режима split
1.322:
	[+] добавлено сравнение распакованных файлов
1.321:
	[!] исправлена распаковка спавнов ЗП
1.32:
	[!] guids.ltx больше не нужен без ключа -idx
	[+] реализована сортировка way-объектов по алфавиту
	[+] реализовано определение принадлежности way-секции по gvid и префиксу
	[+] реализована сортировка alife-объектов
1.31:
	[!] изменен алгоритм сканирования конфигов
	[!] изменен приоритет запросов из clsids.ini, теперь данные из него перекрывают внутренние таблицы
	[!] скрипт адаптирован под новую систему отладочных сообщений
	[i] поправлена логика работы с пакетом актора в ЧН
	[+] добавлена возможность вынесения лога в файл
	[+] добавлена возможность вынесения сетов в отдельный конфиг-файл
1.30:
	[i] теперь новая версия обработчика ошибок по-прежнему обрабатывает спавн Народной солянки
	[i] исправлены проблемы распаковки level.spawn некоторых билдов
1.29:
	[i] исправлена ошибка распаковки level.spawn билдов
	[i] исправлена ошибка распаковки спавна ЧН
	[i] всякие мелкие правки
1.28:
	[i] исправлено игнорирование парсером ключа -way в режиме split.
	[i] исправлена ошибка сканирования конфигов при компиляции.
	[i] исправлена ошибка чтения секций некоторых se-классов.
	[i] исправлена ошибка разбивки спавна, из-за которой генерировались левел спавны неправильного формата.
	[+] добавлен контроль дупликатов актора при компиляции.

1.27:
	[i] исправлена ошибка парсера, в некоторых случаях приводившая к порче логики.
	[i] исправлено создание папок при сохранении результата.
	[+] добавлена переинициализация параметров секции после смены класса при конвертации. Это расширяет диапазон
версий, доступных для конвертирования.
	[+] добавлена поддержка маск при конвертации.
	[+] добавлен ключ -ini в режиме конвертации

1.26:
	[i] поправлена распаковка спавнов ЧН.
	[+] добавлено автоматическое заполнение версии спавна из первой секции (если актора в спавне нет).
	[+] что-то еще по мелочи, не помню.

1.25:
	[i] отключен вывод пустого параметра spawned_obj при распаковке.
	[+] реализовано автоматическое заполнение параметров version и script_version при запаковке спавнов с секциями из 
разных версий игры. Версия берется из конфига актора.

1.24:
	[i] исправлена распаковка/запаковка спавна билда 2571.
	[i] исправлена запись guids.ltx
	[i] мелкие правки

1.23b:
	[+] убрано предупреждение "state data left" при распаковке спавнов ЗП, запакованных
ранее с помощью acdccop.
	[i] исправлены ошибки split, из-за которых могли получаться кривые level.spawn
	[i] переделана логика чтения/записи пакетов se_stalker/se_monster
	[i] мелкие изменения

1.22b:
	[+] добавлен ключ -nofatal

1.21b:
	[i] исправлены небольшие опечатки в коде.
	[i] парсер теперь корректно читает значения с комментариями.

1.2b:
	[+] небольшие правки по конвертации.	
	[+] добавленные в модах соответствия clsid -> серверный класс теперь редактируются
в отдельном конфиге (clsids.ini).
	[+] ошибка при встрече незнакомого clsid теперь выдается при распаковки секции
спавна с таким clsid, а не при сканировании конфигов, как раньше.

1.1b:
	[+] проверена распаковка билд-спавнов, решена проблема декомпиляции спавнов
 билдов 25хх.
	[+] добавлен контроль наличия параметра version в секциях распакованного спавна.
	[i] исправлено исключение файла со spawn_id объектов при сканировании конфигов.

1.0b:
	[+] основательно переработан код, часть скрипта вынесена в отдельные модули.
	[i] исправлены все неработавшие функции.
	[+] увеличена скорость выполнения кода, уменьшены требования по памяти.
================================================================
Копирайты:acdc для ТЧ - bardak, для ЗП - bardak, Kolmogor. Все остальное - K.D.
Используйте/выкладывайте где и как хотите, с указанием авторов.
