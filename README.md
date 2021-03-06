# Факторы, влияющие на рейтинги гостиниц и других объектов размещения в разных регионах России

Анализируются статистики отелей и других объектов размещения (рейтинги, количество отзывов и т.п.) и тексты отзывов их посетителей в разных регионах России. Для примера взяты С.-Петербург, Сочи и Южный Берег Крыма. Данные взяты с сайта tophotels.ru. Код парсера позволяет получить данные и по другим регионам.
После применения модели TF-IDF, а затем – логистической регрессии к текстам отзывов, получаем слова, характерные для разных оценок и в разных регионах. Сравнивая топ-50 слов для каждой оценки и для каждого региона, находим, какие именно слова характерны для хороших и плохих оценок в С.-Пб., Сочи и на ЮБК.

## Ноутбуки, последовательность работы с ними и их функционал:

1) **hotel_data_parsing_auto.ipynb** – парсинг общей информации об отелях: название; район; рейтинг; количество отзывов; ссылка на отзывы. Применяется отдельно для каждой локации (вначале задается переменная main_link, соответствующая списку отелей для заданной локации). Есть задел для вызова из командной строки, но корректная работа не гарантирована.
**На выходе:**

  SPBhotel_stats_clean.csv

  Sochi_hotel_stats_clean.csv

  Crimea_hotel_stats_clean.csv

  UBK_hotels_data_cleaned.csv
  
  ___

2) **hotel_review_parsing_auto.ipynb** – парсинг отзывов на отели (применяется отдельно по каждой локации по данным, полученным на предыдущем шаге и сохраненным в файлы):
ссылка на отзывы, по которой была получена информация, и которая содержит уникальный идентификатор отеля; id пользователя оставившего отзыв; оценка, которую поставил в своем отзыве пользователь; Дата отзыва; в сыром формате – дата и время, в очищенном – только дата в pandas datetime (эта колонка добавлена последней в очищенном формате); time_delta – время в днях, прошедшее с момента проживания пользователя в отеле до момента написания отзывы. Для time_delta> 1 месяц рассчитывалось приблизительно (+- месяц) в очищенной версии файла (в сырой сохранен оригинал); текст отзыва. Есть задел для вызова из командной строки, но корректная работа не гарантирована.
**На выходе:**

  SPb_reviews_raw.csv

  Sochi_reviews_raw.csv

  UBK_reviews_raw_from3.csv
  
  ___

3) **hotel_EDA_general.ipynb** – общая чистка и анализ данных сразу по всем регионам. На входе – файлы, полученные на двух предыдущих этапах. Рассматривается статистика по отелям (оценки, количество отзывов, количество отелей), и статистика по отзывам (оценки). Сравнение статистики оценок по отелям и по отзывам. Переход от непрерывной шкалы оценок к дискретной  - «3» - плохо, «4» - средне, «5» - хорошо.
**На выходе** – очищенные данные по отзывам, к которым уже будем применять TF-IDF:

  SPbreviews_cleaned.csv

  Sochireviews_cleaned.csv

  UBKreviews_cleaned.csv

___

4) **ML_with_reviews.ipynb** – обработка текстов отзывов (лемматизация, токенизация, выкидывание стоп-слов), настройка модели TF-IDF на обработанных текстах. (На входе – файлы, с текстами отзывов, полученные на предыдущем этапе). Затем – применение логистической регрессии, вытаскивание топ-50 важных признаков (слов) для каждой оценки для каждого региона.
**На выходе** – файлы со словами, попавшими в топ для каждой оценки и каждого региона, и с коэффициентами логистической регрессии для этих слов:

  Sochi3.csv, Sochi4.csv, Sochi5.csv,
  
  SPb3.csv, SPb4.csv, SPb5.csv,
  
  UBK3.csv, UBK4.csv, UBK5.csv

___

5) **words_dont_come_easy.ipynb** – визуализация и сравнение топ-50 слов, характеризующих хорошие, средние и плохие отзывы в разных регионах.

___

6) LDA_with_reviews.ipynb – попытка настроить тематическое моделирование. Пока неудачно, возможно, будет продолжено.

___

7) Review_anomalies.ipynb – попытка найти аномалии среди текстов отзывов. Пока не очень удачно, возможно, будет продолжено.

___
___

## Файлы данных и их структура:

**SPBhotel_stats_clean.csv**

**Sochi_hotel_stats_clean.csv**

**Crimea_hotel_stats_clean.csv**

**UBK_hotels_data_cleaned.csv**

**Колонки:** title - Название отеля, area - район (внутри региона), rating - рейтинг, n_reviews - количество отзывов, reference - ссылка на подробное описание

___

**SPb_reviews_raw.csv**

**Sochi_reviews_raw.csv**

**UBK_reviews_raw_from3.csv**

**Колонки:** reference  - ссылка на подробное описание (по ней можно идентифицировать отель в предыдущем файле), user - id пользователя, оставившего отзыв, mark - оценка (которую поставил пользователь в этом отзыве), date - дата отзыва, time_delta - время между посещением объекта размещения и датой отзыва, text - текст отзыва
___

**SPbreviews_cleaned.csv**

**Sochireviews_cleaned.csv**

**UBKreviews_cleaned.csv**

**Колонки:** reference,user,mark,time_delta,text – так же, как и в предыдущей группе файлов; conv_date – дата отзыва, конвертированная в pandas.DateTime без учета времени.

___

**Sochi3.csv, Sochi4.csv, Sochi5.csv,**

**SPb3.csv, SPb4.csv, SPb5.csv,**

**UBK3.csv, UBK4.csv, UBK5.csv**

**Колонки:** words – слова; K – коэффициент, присвоенный в логистической регрессии (чем больше – тем важнее слово для данной оценки и данного региона).

___

**SPbreviews_tokenized.csv**

**Sochireviews_tokenized.csv**

**UBKreviews_tokenized.csv**

**Колонки:** все, как в SPbreviews_cleaned.csv, Sochireviews_cleaned.csv, UBKreviews_cleaned.csv, только теперь предобработанный лемматизированный и токенизированный текст.
