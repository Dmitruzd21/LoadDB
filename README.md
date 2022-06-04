# **Проведение нагрузочного тестирования DB**

**Ссылка на задание/требования:** https://github.com/mshegolev/loadqa-homeworks/blob/main/4.Load%20db/homework_lecture4.md

**Настройки WP:**

- Set Title: hl_middle_qa
- Username: qauser
- Password: A&FMkMjxMBrWrxa6hy
- Search engine visibility: -
- Язык: руский

**Параметры подключения к БД через DBeaver:**

- Server Host: localhost
- Port: 3500
- Database: wpdb
- Username: wpuser
- Password: wppassword

**Параметры подключение к БД через JMeter (JDBC Connection Configuration):**

- DatabaseURL: jdbc:mysql://localhost:3500/wpdb
- JDBC Driver Class: com.jdbs.mysql.Driver
- Username: wpuser
- Password: wppassword

**Ссылка на SQL процедуры:** https://github.com/Dmitruzd21/LoadDB/blob/master/procedure.sql

## **Tестирование процедуры dorepeat_v1 в JMeter**

**Файл в формате jmx:** https://github.com/Dmitruzd21/LoadDB/blob/master/procedure_v1/dorepeat_v1.jmx

**Обычные условия**

- Количество тредов: 100
- Количество повторений: 100
- Количество созданных комментариев: 10 000

**Screenshots:**

![1](/procedure_v1/notManyInserts/pr1_1.png)

![2](/procedure_v1/notManyInserts/pr1_2.png)

![3](/procedure_v1/notManyInserts/pr1_3.png)

![4](/procedure_v1/notManyInserts/pr1_4.png)

**Время отклика вызова процедуры:**

- Среднее: 277 мс

- Медиана: 291 мс

**Создание 552 576 комментариев**

- Количество тредов: 100
- Количество повторений: 5 525
- Количество созданных комментариев: 552 576

**Screenshots:**

![1](/procedure_v1/tooManyInserts/pr1_5.png)

![2](/procedure_v1/tooManyInserts/pr1_6.png)

![3](/procedure_v1/tooManyInserts/pr1_7.png)

![4](/procedure_v1/tooManyInserts/pr1_8.png)

![5](/procedure_v1/tooManyInserts/pr1_9.png)

**Время отклика вызова процедуры:**

- Среднее: 321 мс

- Медиана: 303 мс

**Вывод:** количество строк в таблице wp_comments не влияет на время отклика

## **Tестирование процедуры dorepeat_v2 в JMeter**

**Файл в формате jmx:** https://github.com/Dmitruzd21/LoadDB/blob/master/procedure_v2/dorepeat_v2.jmx

**Screenshots:**

![1](/procedure_v2/notManyInserts/pr2_1.png)

![2](/procedure_v2/notManyInserts/pr2_2.png)

![3](/procedure_v2/notManyInserts/pr2_3.png)

![4](/procedure_v2/notManyInserts/pr2_4.png)

![5](/procedure_v2/notManyInserts/pr2_5.png)

**Время отклика вызова процедуры:**

- Среднее: 486 мс

- Медиана: 432 мс

**Вывод:** время отклика на процедуру 2 больше, чем время отклика на процедуру 1.

## **Тестирование WEB + DB в JMeter**

В одном тест плане 2 потока у каждого потока свои сценарии. Группа сценариев:

Сценарий 1.

- Пользователь читает комментарии к посту https://localhost/привет-мир, обновляя страницу.

Сценарий 2.

- Через БД заполнять комментариями WP 100 комментариев в 1 секунду.
- Опеределить точку отказа, при каком количестве комметариев к посту, у пользователя страница ничинает загружаться больше 20 секунд.

**Файл в формате jmx:** https://github.com/Dmitruzd21/LoadDB/blob/master/httpAndDB/httpAndDB.jmx

### **Первая точка отказа (при каком количестве комметариев к посту, у пользователя страница ничинает загружаться больше 20 секунд)**

**Screenshots:**

![1](/httpAndDB/point1/100hps.png)

![2](/httpAndDB/point1/9694.png)

![3](/httpAndDB/point1/reportWhen9694.png)

![4](/httpAndDB/point1/autoshutdown.png)

**Вывод:** если в таблице появляется около 9694 записей, то пользователи будут ожидать загрузку страницы с комментариями более/равно 20 сек.

### **Вторая точка отказа (невозможность добавить комментарий через графический интерфейс):**

Было обнаружено, что при появлении большого количества комментариев исчезает возможность добавить комментарий к посту через графический интерфейс.

При пустой таблице:

![before](/httpAndDB/point2/WPwithForm.png)

После добавления большого количества комментариев:

![after](/httpAndDB/point2/after.png)

Был добавлени Response Assertion, который проверяет первое слово заголовка "Добавить комментарий" в теле ответа:

![ResponseAssertion](/httpAndDB/point2/ResponceBodyAssertion.png)

Результаты:

![tree](/httpAndDB/point2/36592tree.png)

Параллельно был запущен тред, в котором запрашивалось количество строк в таблице с комментариями:

![count](/httpAndDB/point2/36592_2.png)

**Вывод:** если в таблице появляется около 36592 записей, то пользователи не смогут добавить комментарий в графическом интерфейсе.
