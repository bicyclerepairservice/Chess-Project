# Проект по анализу шахматных партий с применением методов машинного обучения
## Основная информация о проекте

---------

### Общая информация по репозиторию

Ссылка на файлы с данными: https://disk.yandex.com/d/EGHi7eWtsn3Vvw
Объем файлов большой, поэтому они были выгружены в отдельную папку на гугл-диске
В репозитории на данный момент представлено 2 файла формата .ipynb, в которых содержится код для соответствующих разделов исследвоания:

1. Файл 'Parsing.ipynb' содержит код по преобразованию сырого фалйна с шахматными играми формата .pgn в несколько файлов .csv таблиц. Все эти файлы находятся в приложенной выше ссылке
2. Файл 'ForkliftCertified.ipynb' содержит код работы с полученными датасетами. Он включает минимальные преобразования, визуализацию и некоторые выводы о полученных данных

Также репозиторий включает папку с прочими материалами

---------


### Общая информация по датасету

1. Первичные данные

Изначальный датасет с играми был собран с сайта https://database.lichess.org. В нем собраны датасеты по играм за каждый месяц начиная с января 2013 года. В ходе работы было установлено, что с имеющимися мощностями и доступным временем, необходимо выбирать наиболее укороченные датасеты. В ходе распаковки игрового файла .pgn.zst в .pgn его объем увеличивается в несколько раз (в данном исследовании с 48 MB до 237 MB). Был выбран датасет за Август 2013 года, содержащий в себе около 325,000 шахматных игр. На данном этапе была произведена только распаковка формата .zst с помощью архиватора PeaZip

2. Дальнейшее преобразование

На первом этапе парсинга игрового формата была применена библиотека pgn2data, с помощью которого все игры удалось за несколько часов преобразоватьв 2 датасета (они представлены на Яндекс-Диске): один файл, содержащий ходы каждой партии - 'lichess_db_standard_rated_2013-08_moves.csv', и другой файл, содержащий общую информацию об игре - 'lichess_db_standard_rated_2013-08_game_info.csv'

После этого был произведен парсинг первичного датасета для выделения из него двух (пока что) важных признаков, которые впоследствии будут являтся основой для проверки гипотез и проведения машинного обучения: дебюты и оценки ходов в каждой игре. Для этого были применены различные методы. Код данного эатапа работы представлен в репозитории: 'Parsing.ipynb'. 

Одной из проблем датасета было то, что оценка ходов находилась только в 1650 играх из 325,00 - именно поэтому итоговый датасет был укорочен в соответствии с наличием %evals

Также было удалено большое количество колонок в файле с описанием игры

После всевозможных преобразований на выходе было получено два готовх датасета: 'upd_moves_df.csv' и 'upd_games_df.csv'

3. Содержание преобразованного датасета

* Датасет с описанием игр

Он содержит 1651 строку (количество игр) и 11 колонок (признаки):

1. game_id - ID игры, которое создалось в процессе парсинга

2. event - режим шахматной игры

В данном датасете он представлен тремя категориями: Blitz, Bullet и Classic. Также в датасете имеются как отдельные игры, так и игры в рамках чемпионатов

3. white - ник белого игрока

4. black - ник черного игрока

5. result - кто победил (1 - белые, 0 - черные)

6. white_elo - рейтинг белого игрока

7. black_elo - рейтинг черного игрока

8. eco - закодированное название дебюта

9. termination - Как игра закончилась (Истекло время или по ходу игры)

10. time_control - временной режим партии

11. op - название дебюта

12. winner_loser_elo_diff - разница в рейтинге между победителем и проигравшим

* Датасет с ходами

Он содержит 65 колонок, основными из которых являются:

1. eval - оценка хода

2. piece - шахматная фигугра, которой делается ход

3. move_no_pair - порядковый номер хода (парный)

4. Количетсво оставшихся фигур каждого цвета после каждого хода

5. is_check, is_checkmate, is_gameover - отражает результат игры на текущем ходе


Другие колонки в дальнейшем могут понадобится для образования новых признаков

---------

### Визуализация

Данный раздел представлен в файле 'ForkliftCertified.ipynb'

----------

### Гипотезы

coming soon...


---------------

## Машинное обучение

В кажестве входных данных модель будет принимать характеристику партии (дебют, разница в рейтинге победителя и проигравшего, режим игры и т.д.), ее основные признаки, а также характеристику ходов (динамика eval, количетсво использванных фигур, количество ходов и т.д.). На выходе модель должна предсказать рейтинги для обоих игроков на основе полученных признаков.
