# Проект по анализу шахматных партий с применением методов машинного обучения для предсказания рейтинга игроков
## Основная информация о проекте

Авторы:
Адилхан Санжар, БЭК212
Гусев Владислав, БЭК212
Преподаватель:
Воропаев Роберт Ираклиевич

---------

### Общая информация по репозиторию

Ссылка на файлы с данными: https://disk.yandex.com/d/EGHi7eWtsn3Vvw
Объем файлов большой, поэтому они были выгружены в отдельную папку на гугл-диске
В репозитории на данный момент представлен файла формата .ipynb, в которых содержится код для соответствующих разделов исследвоания:

**В папке First Stage:**

1. Файл 'Parsing.ipynb' содержит код по преобразованию сырого фалйна с шахматными играми формата .pgn в несколько файлов .csv таблиц. Все эти файлы находятся в приложенной выше ссылке
2. Файл 'ForkliftCertified.ipynb' содержит код работы с полученными датасетами. Он включает минимальные преобразования, визуализацию и некоторые выводы о полученных данных

Оба эти файла были необходимы для первичного исследования данных и оценки возможных перспектив провекта. На данный момент они не являются актуальными, однако все же имеют ценность

**В основном разделе:**

1. Файл "Parsing and preprocessing, better version.ipynb" представляет из себя обновленную предобработку данных в приемлемый датафрейм шахматных игр
2. Файл "Visualisation and hypotheis testing.ipynb" представляет из себя разведочный анализ, совмещающий в себе как остаточную предобработку данных, ее визуализацию и формирование из всей этой информации новых признаков. Более того, данная часть включает проверку гипотез, выдвинутых в ходе проекта
3. Файл "....", который будет включать в себя финальную обработку данных для предсказания новых признаков. В нашем случае - навыки игроков в соответствии с классификацией FIDA. Данную классификацию можно найти в файле "elo_ratings.png". Однако данная классификация является исключительно ориентиром
4. Сам файл "elo_ratings.png"

**На диске:**

1. First stage, data

  1.1 "lichess_db_standard_rated_2013-08.pgn" - сырые данные по шахматным играм за август 2013
  
  1.2 "evals.csv" - извлеченные оценки для каждого хода в каждой игре
  
  1.3 "lichess_db_standard_rated_2013-08_game_info.csv" и "lichess_db_standard_rated_2013-08_moves.csv" - преобразованные с помощью библиотеки PGN2Data имеющихся датасетов с играми
  
  1.4 "upd_games_df.csv" и "upd_moves_df.csv" - итоговые таблицы с необходимыми характеристиками. О них подробнее в следующих разделах
 
2. Final stage, data
(В связи с тем, что большинство файлов в работе слишком большие, то на диск будут загружены последние наибоее обоработанные и укороченные файлы. Код по промежуточным файлам можно найти в файле "Parsing and preprocessing, better version.ipynb". Для обработки всех тех файлов понадобилось использовать два компьютера одновременно)
  
  2.1 "all_info.csv" и "all_moves" - таблицы, содержащие примерно 11 000 игр, которые практически готовы для финальных этапов проекта (машинное обучение)
  
  2.2 "massive_fazan.pgn" и "massive_peacock.pgn" - файлы, содержащие 200 000 партий каждый. В ходе исследования именно они преобразуются и по итогу склеиваются в один (об этом в следующих разделах)

---------


### Общая информация по датасету

#### 1. Первичные данные

Изначальный датасет с играми был собран с сайта https://database.lichess.org, где данные представлены в формате .pgn.zst. .pgn - это формат шахматной нотации, в котором указаны данные игроков и ходы, причём 6% всех игр имеют оценку ходов шахматным движком Stockfish. Тут собраны датасеты по играм за каждый месяц начиная с января 2013 года. В ходе работы было установлено, что с имеющимися мощностями и доступным временем, необходимо выбирать наиболее укороченные датасеты, писать на них код, а потом переносить всё на большой датасет. 

В ходе распаковки игрового файла .pgn.zst в .pgn его объем увеличивается в несколько раз (в данном исследовании с 48 MB до 237 MB). Был выбран датасет за Август 2013 года, содержащий в себе около 325,000 шахматных игр. На данном этапе была произведена только распаковка формата .zst с помощью архиватора PeaZip.

В итоговом исследовании был выбрал датасет от мая 2017 года, который весит в распакованном виде около 24 Гб, однако из него были взяты только 400 000 партий. А после большого количества преобразований их осталось 11 000.

#### 2. Этапы преобразования

В данном разделе остановимся именно на преобразовании файлов из "Final stage", так как они в целом включают в себя все предыдущие разделы.

#####  1. Извлечение партий из основного датасета

Изначально единственное, что у нас было - это 24 Гб файл с шахматными партиями за май 2017 года. Очевидно, что преобразования такого большого файла с имеющимися "любительскими" мощностями компьютера не представляется возможным. Именно поэтому в 2 этапа из данного файла были вытащены по 200 000 партий и записаны в файлы. Файл со всеми играми невозможно представить даже на диске, однакодва полученных датасета как раз лежат в папке "Final stage, data"

#####  2. Преобразование .pgn файлов в формат .csv

На втором этапе парсинга игрового формата была применена библиотека pgn2data, с помощью которого все игры удалось за несколько часов преобразовать в 2 датасета (они они сликом большие для диска): один из них содержит всю основную информацию по играм, а другой - всю информацию по ходам. До предобработки там оказывалось большое количество пустых колонок / ненужной информации. Единственная сложность этого этапа заключалась во времени преобразования этих датасетов, так как они при переносе формата увеличиваются по объему более чем в 10 раз (400 Мб -> 5.5 Гб)

#####  3. Извлечение дополнительных харакеристик, которые не были захвачены библиотекой pgn2data

После этого был произведен парсинг первичного датасета (двух файлов .pgn) для выделения из него трех важных признаков, которые впоследствии будут являтся основой для проверки гипотез и проведения машинного обучения:   **дебют** (op) и **оценки ходов** (eval) и **время ходов** (clk) в каждой игре. Крайне неоптимальный код по извлечению данных характеристик представлен в репозитории: Код данного эатапа работы представлен в репозитории: "Parsing and preprocessing, better version.ipynb". 

Одной из проблем датасета было то, что оценка ходов находилась только в крайне незначительной доле игр (это и следовало из документации), именно поэтому объем данных в несколько раз сократился после извлечения такого рода игр. Однако это являтся плюсом, так как дальнейшие операции будут проходить намного быстрее. Ну и нельзя не сказать, что все машинное обучение и проверка гипотез в целом будут основаны на данном параметре, так как он являся основным показателем навыков игрока. Таким образом, итоговый датаасет был укорочен в соответствии с наличием %evals (оценками ходов).

#####  4. Дополнительные модификации выведение основных признаков

Дополнительная фильтрация произошла по режимам игр. В исходной таблице были представлены Bullet, Blitz, Ultrabullelt Games и соответсвующие турниры, средний рейтинг в которых в связи с особенностями каждого режима серьезно разнится.
Как видно из этого скриншота лидеров по рейтингу в каждой из категорий, максимальное значение и разброс этих значений сильно колеблятся от периода к периоду. Была идея сравнить рейтинги в каждом из режимов и проверить какие-то гипотезы, но мы не считаем это необходимым в данном проекте

![image](https://github.com/bicyclerepairservice/Chess-Project/assets/133600177/880a20e6-3498-493c-b4e7-a154fbf0ad0b)
Источник: https://lichess.org/player

Именно поэтому внимание было уделено только обычным Классическим играм - их суммарно в обоих датасетах оказалось чуть более 11 000. Кстати, эти датасеты представлены на гугл-диске в папке Final Stage, data: 'all_info.csv' и 'all_moves.csv'. 

##### 1. Содержание преобразованного датасета

Таким образом, мы рассматриваем выборку из игр классического режима в от мая 2017 года, обладающих оценкой Stockfish. Всего имеется ровно 11 431 партий и каждая из них обладает следующим набором признаков:

* Датасет с описанием игр

1. game_id - ID игры, которое создалось в процессе парсинга

5. result - кто победил ('1-0' - белые, '0-1' - черные, '1/2-1/2' - ничья)

6. white_elo - рейтинг белого игрока

7. black_elo - рейтинг черного игрока

8. winner_loser_elo_diff - разница в рейтинге между победителем и проигравшим

8. eco - закодированное название дебюта

9. termination - Как игра закончилась (Истекло время или по ходу игры)

10. time_control - временной режим партии

11. op - название дебюта (в целом нужно только для визуализации)

* Датасет с ходами (он являтся вспомогательным и из него в процессе создания новых признаков партий будут вытащены необходимые элементы

Он содержит 65 колонок, основными из которых являются:

1. eval - оценка хода

2. piece - шахматная фигугра, которой делается ход

3. move_no_pair - порядковый номер хода (парный)

4. Количетсво оставшихся фигур каждого цвета после каждого хода

5. is_check, is_checkmate, is_gameover - отражает результат игры на текущем ходе

6. clk - время, которые было у игрока на момент хода

Другие колонки в дальнейшем могут понадобится для образования новых признаков, но в README описывать их нет надобности

----------

### Визуализация

Данный раздел представлен в файле 'Visualisation and hypotheis testing.ipynb'. На данном этапе проводится разведочный анализ данных для выявления зависимостей / интересных наблюдений, которые впоследствии нужно будет статистически обосновать (проверка гипотез)

----------

### Создание новых признаков

#### 1. Категории игроков по среднему рейтиингу партии
Оказалось, что классификация,представленная в репозитории, слишком точно определяет категории, именно поэтому придется применить собственную классификацию:

1. Меньше 1350 - новички

2. 1350 - 1700 - средние игроки

3. 1700 - 1900 - высокоуровневые игроки

4. Выше 1900 - крайне сильные игроки

#### 2. Категории игр по переменной time_control

На этом этапе  придется сократить исходный датасет игр, так как существуют режимы по типу 10800 секунд или 60. В контексте исследования ходов и их оценок будет нерационально брать такие отклоняющиеся значения (выбросы). Возьмем только те режимы, которые встречались в >100 партиях. Соответствующим образом сократим исходные датафремы с играми.

#### 3. Категории дебютов по переменной eco
ECO -  Encyclopedia of Chess Openings, классифицирует дебюты по буквам латинского алфавита от A до E и имеющие "подкатегории" с числами от 1 до 99:

**Категория A:** Флангловые дебюты

Игра характеризуется асимметричными позициями и нестандартными пешечными структурами. Фланговые дебюты часто нацелены на реализацию стратегических идей, таких как контроль над определенными площадями, атака с флангов или поиск нетрадиционных расстановок фигур.

**Категория B:** Полуоткрытые дебюты

Наиболее ярким примером является сицилийская защита (1.e4 c5), известная своими асимметричными и динамичными позициями. Черные стремятся контратаковать в центре или на флангах, используя "застрявыше пешки" (pawn break) и активно используя другие фигуы.

Pawn Break наглядно:
![image](https://github.com/bicyclerepairservice/Chess-Project/assets/133600177/6bf00995-8aa7-416b-8ea0-42f92a55a251)
Источник:https://chess.stackexchange.com/questions/1181/what-is-a-pawn-break

**Категория C** Открытые дебюты

В эту категорию входят дебюты, в которых оба игрока начинают с 1.e4 e5. Открытые позиции часто возникают из-за напряженности в центральной пешке и возможностей для прямого взаимодействия фигур. Тактические осложнения часто возникают из-за наличия открытых линий и сбалансированной пешечной структуры.

**Категория D:** Закрытые дебюты
Созданы для установления мощной структуры из пешек в центре доски. Эти дебюты также основаны на намеренных pawn breaks и различных стратегических планов по развитию фигур. Различные пешечные структуры и стратегические идеи могут привести к различным типам позиций, таким как симметричные или асимметричные пешечные структуры.

**Категория E:** Индийские защиты

В этих дебютах часто приоритет отдается гибкости и не допускаются pawn breaks. Стратегия черных может включать в себя контроль над центром на расстоянии, подготовку конкретных пешечных ходов или гармоничное размещение фигур перед реализацией стратегии.






----------

### Проверка гипотез

Сначала пройдемся по базовым гипотезам:

1) Проверим, равны ли матожиданя рейтингов игроков за белых и за черных, или все же существуют некоторые различия

2) Гипотеза о том, что корреляция между средним рейтингом в партии и средним отклонением %eval на первом этапе игры равна 0.7

Имея данный датасет, можно придумать очень большое количество идей для проверки интересных гипотез. Исходя из имеющихся данных по часам игроков во время партии ( потраченное время на обдумывание (%clk)), возможно проверить ряд утверждений:

1) Неопытные игроки тратят намного меньше времени на ход, чем профессионалы (например, имея изначально на часах 10 минут, партия может завершиться за 2 минуты). Гипотеза о равенстве среднего времени для хода игроков из 1 категории (<1350) и 4 категории (>1900)

3) Чем меньше времени остаётся у игрока - тем хуже он начинает играть. Как время влияет на оптимальность хода? Гипотеза о том, что в первой половине игры игроки чаще начинают ошибаться и отклонение %eval становится больше


----------

### Машинное обучение

В исследовании используется два алгоритма машинного обучения, выполняющих задачу классификации: Random Forest Classifier и Gradient Boosting Classifier, чтобы она по ходам в партии двух игроков определяла категорию, в которую по среднему рейтингу попали игроки (данная классфикация представлена в разделе "Создание новых признаков" 

У нас имеются следующие идеи по признакам:
1) Разделить партию на 3 части (дебют, миттеншпиль, эндшпиль). Очевидно, что профессионалы играют на равных в дебюте и середине и начинают ошибаться только под конец, когда как в низкорейтинговых матчах видны огромные колебания перевеса. Отсюда следует второй признак
2) Количество ходов, за которое закончилась партия. Опытные игроки никогда не проиграют за 3 хода. Чем больше рейтинг игроков, тем они внимательнее, и, соответственно, партия становится длиннее.
3) Данные с часов. Например, можно добавить признак: "процент израсходованного времени". То есть, если у человека было 10 минут, а партия закончилась, когда у игрока оставалось 9 минут, то процент израсходованного времени - 10%. Это очень мало, обычно такие показатели у слабых игроков.
Пока на этом всё, во время обучения модели придумаем что-то ещё.

Касаемо первого пункта: мы планируем классифицировать ходы так (примерно): 
1) Теоретический ход - ход, повторяющий какой-то дебют. Обычно у неопытных игроков теоретические ходы заканчиваются после третьего хода, а у профессионалов могут занять 10 ходов.
2) Неточность
3) Ошибка
4) Зевок

Такая классификация поможет понять, какие ходы делают игроки того или иного рейтинга.
