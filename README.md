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
В репозитории на данный момент представлено большое количество файлов, в которых содержится код для соответствующих разделов исследвоания:

**В папке First Stage:**

1. Файл 'Parsing.ipynb' содержит код по преобразованию сырого фалйна с шахматными играми формата .pgn в несколько файлов .csv таблиц. Все эти файлы находятся в приложенной выше ссылке
2. Файл 'ForkliftCertified.ipynb' содержит код работы с полученными датасетами. Он включает минимальные преобразования, визуализацию и некоторые выводы о полученных данных
3. Файл 'ML & новые признаки.ipynb' содержит один из пробных вариантов проведения классификации на основе имеющихся данных

Все эти файлы были необходимы для первичного исследования данных и оценки возможных перспектив провекта. На данный момент они не являются актуальными, однако все же имеют ценность

**В основном разделе:**

1. Файл "Parsing and preprocessing, better version.ipynb" представляет из себя обновленную предобработку данных в приемлемый датафрейм шахматных игр
2. Файл "Visualisation and hypotheis testing.ipynb" представляет из себя разведочный анализ, совмещающий в себе как остаточную предобработку данных, ее визуализацию и формирование из всей этой информации новых признаков. Более того, данная часть включает проверку гипотез, выдвинутых в ходе проекта
3. Файл "Advanced Machine Learing.ipynb", который будет включать в себя финальную обработку данных для предсказания новых признаков. В нашем случае - навыки игроков в соответствии с классификацией FIDA. Данную классификацию можно найти в файле "elo_ratings.png". Однако данная классификация является исключительно ориентиром...В следующих разделах проекта будет более подробно расписана используемая классификация
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
  
  2.3 "machine_part.csv' - подготовленный датасет со всеми необходимыми признаками для проведения предсказания класса игроков

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

Другие колонки в дальнейшем могут понадобится для образования новых признаков, но в README описывать их нет надобности. Обо всем вы узнаете по ходу прочтения последующих разделов

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

На этом этапе  придется сократить исходный датасет игр, так как существуют режимы по типу 10800 секунд или 60. В контексте исследования ходов и их оценок будет нерационально брать такие отклоняющиеся значения (выбросы). Возьмем только те режимы, которые встречались в >40 партиях. Соответствующим образом сократим исходные датафремы с играми. 

Для этого будут выбраны следующие временные режимы: 

600   ->   5464 шт.

900   ->   2308 шт.

300   ->   1004 шт.

480   ->   576 шт.

1200  ->   535 шт.

1800  ->   365 шт.

1500  ->    195 шт.

420   ->    184 шт.

720   ->    145 шт.

360   ->    100 шт.

540   ->    93 шт.

180   ->    61 шт.

3600  ->    46 шт.

2700  ->    42 шт.


Остальные единичыне случаи только ухудшат качество модели, поэтому отформатируем датафрем соответственно

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

#### 4. Количетсво ходов за игру

Данный признак демонстирует классы игроков - ни один профессионал не проиграет за 10 ходов. Более того, чем больше ходов делается, тем более равными по идее оказываются соперникик

#### 5. Стандартное отклонение оценок ходов (eval) в каждой трети игры

Как будет упоминаться в разделе "Машинное обучение", каждую игру можно разбить на 3 этапа: дебют, миттеншпиль, эндшпиль. Стандартное отклонение в данном случае будет демонтрировать неравность игроков / количество и значимость ошибок, которые игроки совершают во время игры в зависимости от приблежения к концу. Приходится оперировать более агрерированным категориями, однако результат должен быть хорошим

#### 6. Динамика потери фигур в каждой трети игры

Этот признак опять же определяет класс игрока: новички в на первых же ходах начнут забирать друг у друга фигуры, в то время как высокоуровневые игроки по мере формирования своей стратегии не дают никаких шансов соперику забрать у них фигуру

#### 7. Средний eval за игру

Возможно, полезный признак, так как он в целом отражает кто из игроков доминировал во время игры и в какой степени (продвинутые игроки все же более "равны" в играх)

#### 8. Cтандартное откронение времи, оставшееся после хода в каждой трети игры

В плане интервпретации данной величины могут возникнуть неточности, оданко данный показатель конркетно будет демонстрировать, насколько больше/меньше игроки думают в зависимости от части игры, то есть приближения к временному ограничению


# Итого, в финальном датасете присутствуют следующие признаки для каждой партии:

1. game_id - уникальный идентификатор игры, строка
2. termination - как игра закончилась, категориальная переменная
3. result_coded - кто победил, категориальная переменная
4. skill_cat - уровень игроков в партии в соответствии с их средним рейтингом, категориальная переменная
5. seconds - отведенное время на ходы для каждого игрока, int
6. eco_cat - дебют, категориальная переменная
7. 0 - количество ходов за игру, int
8. eval_std1, eval_std2, eval_std3 - стандартные отклонения оценок ходов в каждой из 3 частей партии (дебют, миттеншпиль, эндшпиль)
9. loss1, loss2, loss3 - динамика потери фигур в каждой части партии
10. mean_eval - средняя оценка хоров в партии
11. vremya1, vremya2, vremya3 - стандартное откронение времи, оставшееся после хода в каждой части партии

Данный файл представлен на Яндекс-диске под названием "machine_part.csv'

----------

### Проверка гипотез

Сначала пройдемся по базовым гипотезам:

1) Проверим, равны ли матожиданя рейтингов игроков за белых и за черных, или все же существуют некоторые различия

Имея данный датасет, можно придумать очень большое количество идей для проверки интересных гипотез. Исходя из имеющихся данных по часам игроков во время партии ( потраченное время на обдумывание (%clk)), возможно проверить ряд утверждений:

2) Чем меньше времени остаётся у игрока - тем хуже он начинает играть. Как время влияет на оптимальность хода? Гипотеза о том, что в первой половине игры игроки чаще начинают ошибаться и отклонение %eval становится больше. 

3) Гипотеза о равенстве вероятностей победы у чёрных и белых. Мы проверили эту гипотезу 2 способами: обычный Z-тест о пропорции и наивный бутстрап с 10000 итерациями.
----------

### Машинное обучение

В исследовании используется два алгоритма машинного обучения, выполняющих задачу классификации: Random Forest Classifier и Gradient Boosting Classifier, чтобы она по ходам в партии двух игроков определяла категорию, в которую по среднему рейтингу попали игроки (данная классфикация представлена в разделе "Создание новых признаков" 

Получившийся результат для Случайного леса:

![91513079-1e1f-466b-90f6-5684f07e5b5e](https://github.com/bicyclerepairservice/Chess-Project/assets/133600177/c71e9b8b-e5f5-4869-8c88-3d2044545681)

Получившийся результат для Градиентного бустинга:

![image](https://github.com/bicyclerepairservice/Chess-Project/assets/133600177/6a890a37-b780-4d13-b5b3-0659d1d3da3d)

Выводы: можно заметить, что модель лучше всего обнаруживает игроков Класса 0 и 3 (профессионалов и новичков), однако имеет трудности с классификацией игроков Класса 1 и 2. Скорее всего это связано с особенностью Lichess: игроки такого рейтинга мало чем отличаются. Поэтому мы видим такое серьезное отличие в классификации профессионалов и новичков.

----------



### Заключение по проекту

Проект является прекрасным примером совмещения двух дисциплин. 

Исследовав множество шахматных партий и проанализировав их с помощью методов машинного обучения, модель научилась классифицировать игроков по уровню игру с очень хорошей точностью. Также мы проверили несколько гипотез. Одна из самых интересных гипотез проверяет взаимосвязь оставшегося времени на часах и уровень игры. Таким образом, мы выяснили, что имея меньше времени на часах, люди склонны чаще ошибаться из-за психологического давления.

Также мы классифицировали и визуализировали популярность проигрываемых дебютов. Самым популярным дебютом является Сицилианская защита. Помимо этого, мы визуализировали частоты "движения" той или иной фигуры. Как оказалось, игроки в шахматы чаще всего двигают пешкой. На втором месте по частоте движения - конь. А самая нечастая фигура - король.

Делая проект, у нас возникло огромное количество идей для дальнейшего развития (причём не только в Data Science, Machine Learning & Deep Learning):
1) Модель можно сделать намного качественнее, используя более обширный датасет. Мы обработали примерно 20 ГБ игр, в то время как в открытом доступе возможно скачать минимум 1.5 ТБ чистых данных с рейтингом. 
2) Из-за того, что шахматный движок Stockfish имеет разные глубины для анализа, некоторые ходы у нас имеют разные оценки, что сильно снижает точность оценки. Таким образом, можно реализовать собственную версию Stockfish в Python, а затем каждый ход оценивать на одинаковой глубине. Это поможет справиться с проблемой того, что одинаковые ходы в одинаковых позициях имеют разные оценки.
3) Можно сделать модель "запоминающей" игроков. В датасете, очевидно, один и тот же игрок встречается несколько раз, что может усилить модель и уверенность в оценке.
4) Глобальная идея: реализовать собственный шахматный движок. Мы немного изучили эту тему, но это требует серьезной подготовки в области алгоритмов и структурах данных. Помимо этого, сложно реализовать подобное на Python, так как он является очень медленным (например, по сравнению с C++ или Java).
