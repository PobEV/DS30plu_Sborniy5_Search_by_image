1. Заказчик проекта - фотохостинг для профессиональных фотографов «Со Смыслом» (“With Sense”). Пользователи сервиса размещают свои фотографии на хостинге и сопровождают их полным описанием: указывают место съёмок, модель камеры и т. д. Отличительная особенность сервиса — описание: его может предоставить не только тот, кто размещает фотографию, но и другие пользователи портала.
<br><br>

2. Поставлена задача:<br>
Необходимо разработать демонстрационную версию поиска изображений по запросу. Для демонстрационной версии нужно выбрать лучшую  модель, которая получит векторное представление изображения, векторное представление текста, а на выходе выдаст число от 0 до 1 — и покажет, насколько текст и картинка подходят друг другу. На основе лучшей модели можно будет собрать предварительную версию продукта, которую презентуют руководителю компании.
<br><br>

3. Выполнена загрузка файлов. Данные загружены верно, значения корректно распределены по столбцам, пропусков или значений в формате даты/времени не замечено:
    - `df_train_dataset` состоит из 5 822 строк и 3 столбцов;
    - `df_crowd_annotations` состоит из 47 830 строк и 5 столбцов;
    - `df_expert_annotations` состоит из 5 822 строк и 5 столбцов;
    - `df_test_images` состоит из 100 строк и 1 столбца;
    - `df_test_queries` состоит из 500 строк и 3 столбцов;
    - количество изображений в тренировочном датасете: 1000;
    - количество изображений в тестовом датасете: 100.
<br><br>

4. Результат исследовательского анализа:
    - пропуски отсутствуют;
    - типизация выполнена верно;
    - явных дубликатов не обнаружено;
    - выполнено преобразование значений доли людей, подтвердивших, что описание соответствует изображению: выражено в % и округлено до 2 знаков после запятой;
    - почти 88% людей, считают, что изображение и описание не подходят друг другу;
    - в опросах людей чаще всего опрашивали 3 человека, что совпадает с количеством оценок экспертов;
    - первый эксперт давал негативную оценку (1 и 2 балла) схожести картинки и описания в 90% случаев;
    - эксперт №2 также фиксировал отсутствие схожести в 86% случаев;
    - третий эксперт оставлял более позитивную оценку, и 21% изображений и описаний были подходящими (3 и 4 балла);
    - выполнили расчёт финальной оценки схожести картинки с описанием, учитывая мнение экспертов и опроса людей;
    - подготовили тренировочный датафрейм.
<br><br>

5. Выполнена проверка на наличие данных (описаний и изображений), включающих лиц не достигших 16 летнего возраста. Записи исключены из обучающего датасета.
<br><br>

6. Проведена векторизация изображений сразу для тренировочного и тестового датасета.
<br><br>

7. Проведена векторизация текстовых описаний сразу для тренировочного и тестового датасета.
<br><br>

8. Выполнено объединение эмбеддингов изображений и текстовых описаний для тренировочного датасета.
<br><br>

9. Осуществлено разбиение данных для обучения на тренировочную и валидационные выборки с помощью класса GroupShuffleSplit.
<br><br>

10. В качестве метрики выбрана RMSE. Выбор RMSE (корня среднеквадратичной ошибки) в качестве метрики обусловлен её простой интерпретируемостью (те же единицы измерения), чувствительностью к большим ошибкам, дифференцируемостью.
<br><br>

11. Для решения задачи регрессии воспользовались тремя моделями: линейная регрессия с регуляризацией - Ridge(), градиентный бустинг - CatBoostRegressor() и Полносвзяная нейронная сеть. В результате расчёт метрики RMSE на кросс-валидации и последующей оценки работы модели на валидационных данных получили следующие результаты:

    |Модель|Гиперпараметры|RMSE на CV*|RMSE на val**|
    |:-|:-|:-:|:-:|
    |Ridge|alpha = 2.5|0,220|0,210|
    |CatBoostRegressor|depth = 6, iterations = 100, learning_rate = 0.18|0,218|0,210|
    |Neural Network|epochs = 500, batch_size = 8000, learning_rate = 0.0005|0,218|0,213|

    <sub><i>
    \* RMSE на CV - метрика, рассчитанная на кросс-валидации (тренировочные данные).<br>
    ** RMSE на val - метрика, рассчитанная на валидационных данных.
    </i></sub><br><br>

    Лучший результат показала модель CatBoostRegressor, которую применили для дальнейшего тестирования.
<br><br>

12. В результате визуального тестирования точность работы модели оставляет желать лучшего. Необходима дальнейшая работа над улучшением качества.