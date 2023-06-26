## Лабораторная работа № 1 "Изучение синхронного инференса с применением веб-сервиса"

### Дисциплина: "Облачные вычислительные системы"

#### Цели и задачи работы:

1. Познакомиться с понятием инференса в системах машинного обучения.
2. Познакомиться с протоколом `HTTP` и микросервисной архитектурой.
3. Изучить принципы сохранения обученной модели.
4. Получить навыки проектирования и разработки веб-сервисов.

#### Теоретические сведения

Обычно система машинного обучения предполагает наличие этапов *обучения* и *инференса*. На первом из них системе подается на вход обучающая выборка, выполняется настройка параметров и подбор гиперпараметров, оценивается качество её работы. Результатом данного этапа является *готовая модель*, которая может быть использована для решения задачи, для которой она создавалась.

**Инференсом** называют процесс получения предсказания обученной модели машинного обучения для новых данных. Инференс принято подразделять на:
* **потоковый**, в котором объекты с их признаковым описанием в достаточно большом количестве поступают потоком, например, с использованием `Kafka`. Для систем потокового инференса важна, в первую очередь, пропускная способность, определяющая количество объектов, обработанных моделью в единицу времени.

* **синхронный**, в котором клиент, отправивший объект в систему для инференса, ожидает ответа. Такие системы обрабатывают объекты поодиночке, но должны делать это быстро, чтобы задержка обработки данных и время ожидания клиента были небольшими.

* **асинхронный**, в котором клиент может оправить объект в систему и сразу отключиться от нее. Позже, он обращается вновь к системе и проверяет, обработаны ли его данные, и, если да, то получает их. Такая модель инференса применяется, как правило, к данным, которые не могут быть обработаны в пределах времени ожидания клиента.

В данной работе студенту предлагается реализовать *синхронную* модель инференса, в которой система, обрабатывающая клиентские данные, представлена в виде *веб-сервиса*.

**Веб-сервисом** называют обладающую уникальным веб-адресом (URL) программную систему, построенную на базе открытых протоколов / стандартов и применяющуюся для обмена данными между приложениями или системами. Различные приложения используют веб-сервисы для обмена информацией друг с другом по компьютерной сети с использованием `HTTP`-протокола. Одним из широко распростаненных архитектурных стилей построения веб-сервисов является `REST`, требующий выполнения определенных принципов, введенных Роем Филдингом:

1. Клиент-серверная модель.
2. Отсутствие сохранения состояния.
3. Единообразие интерфейса.
4. Кэшируемость.
5. Многоуровневая система.
6. Предоставление кода по требованию.

Для работы с ресурсами в REST используют методы HTTP, а информация пересылается между клиентом и сервером с использованием распространенных форматов (чаще всего, JSON или XML).

Спецификацией для описания `REST API` является **[OpenAPI](https://www.openapis.org/)**, ставший стандартом для описания программного интерфейса с большим количеством сопутствующих инструментов и сервисов. Данная спецификация предполагает независимость от языков программирования, и удобна в использовании как человеком, так и программными системами. Для языка Python создан высокопроизводительный веб-фреймворк **[FastAPI](https://fastapi.tiangolo.com/)**, использующий стандарт OpenAPI для определения своего программного интерфейса.

В данной работе сервис инференса получает данные с признаковым описанием и отправляет результаты клиенту в формате JSON.

#### Порядок выполнения работы

1. Создайте папку на компьютере для проекта и склонируйте в нее содержимое репозитория:

> `git clone https://github.com/kpdvstu/CloudCS-Lab1.git`

2. Изучите содержимое проекта. Директория `notebooks` содержит ноутбук для обучения и сохранения модели, в `data` хранится датасет, в `models` — готовые к инференсу модели (данная директория первоначально отсутствует в проекте, она будет создана при выполнении кода в ноутбуке). Папки `src` и `test` содержат исходный код и модульные тесты для веб-сервиса.

3. Изучите содержание ноутбука и выполните все его ячейки. Убедитесь, что модель обучилась с хорошим качеством и сохранилась в папке `models`.

4. Изучите код сервиса для инференса (для удобства его можно открыть в любой среде разработки, например, `PyCharm`).

5. Установите виртуальное окружение с версией питона **3.10** и необходимые зависимости (в терминале или среде разработки):

> `conda create -n cloudsc-env python=3.10`

> `conda activate cloudsc-env`

> `pip install -r requirements.txt`

6. Запустите модульные тесты и убедитесь, что они выполнились успешно:

> `PYTHONPATH=./:./src/ pytest test`

Тесты можно запустить и с использованием встроенных средств среды разработки.

7. Перейдите в директорию с исходным кодом сервиса:

> `cd src/`

8. Запустите веб-сервис для инференса:

> `MODEL_PATH="../models/pipeline.pkl" uvicorn main:app`

9. Проверьте работоспособность сервиса в браузере по адресу http://localhost:8000/docs Открывшуюся страницу можно использовать для отправки запросов сервису и получения ответов от него.

10. С использованием `curl` (или любого другого клиента, например, `telnet`, `PuTTY`, `Postman` и др.) выполните следующие запросы. Дождитесь ответов от сервиса. Обратите внимание, что для выполнения инференса сервис требует авторизации клиента, ожидая от него **Bearer Token**. Корректный токен в данной тестовой задаче — строка из пяти нулей.

> `curl http://localhost:8000/healthcheck`

> `curl -X POST http://localhost:8000/predictions -H "Authorization: Bearer 00000" -H 'Content-Type: application/json' -d '{"cylinders": 4, "displacement": 113.0, "horsepower": 95.0, "weight": 2228.0, "acceleration": 14.0, "model_year": 71, "origin": 3}'`

> `curl -X POST http://localhost:8000/predictions -H 'Content-Type: application/json' -d '{"cylinders": 4, "displacement": 113.0, "horsepower": 95.0, "weight": 2228.0, "acceleration": 14.0, "model_year": 71, "origin": 3}'`

> `curl -X POST http://localhost:8000/predictions -H "Authorization: Bearer 00002" -H 'Content-Type: application/json' -d '{"cylinders": 4, "displacement": 113.0, "horsepower": 95.0, "weight": 2228.0, "acceleration": 14.0, "model_year": 71, "origin": 3}'`

Проанализируйте отправленные запросы и полученные ответы. Сделайте выводы.

#### Индивидуальное задание

*Данная лабораторная работа является составной частью курсовой работы, защищаемой студентами в конце семестра.*
При выполнении лабораторной работы можно использовать **любые** языки и фреймворки, позволяющие выполнить поставленную задачу.

1. **Реализуйте или возьмите готовую** модель машинного обучения, для которой Вы будете реализовывать синхронный инференс, аналогичный рассматриваемому в примере. Модель может быть любой работоспособной, её сложность значения не имеет. Варианты получения модели могут быть любыми:

* написать самостоятельно;
* найти подходящую на [Kaggle](https://www.kaggle.com/) или в любом другом хранилище моделей;
* использовать уже ранее созданную и обученную модель (например, в рамках дисиплины "Машинное обучение и нейросетевые модели" или магистерской диссертации), и т.д.

**!!! При выборе модели обратите внимание, что скрипт или ноутбук, реализующий ее обучение, должны сохранять не только саму модель, но и все этапы предварительной обработки данных в виде, например, `Scikit-Learn Pipeline` (как в рассматриваемом примере). Сохранения только весов обученной модели недостаточно! На вход конвейер должен получать сырые, необработанные данные, а всю их обработку и применение модели к ним должно обеспечиваться различными этапами сохраненного конвейера!** Сохранение конвейера можно дописать к существующей модели, но для этого он должен быть!

2. Обучите выбранную модель, проконтролируйте качество ее обучения и сохраните для дальнейшего использования.

3. Используя представленный в лабораторной работе пример сервиса как шаблон, создайте свой собственный сервис для инференса выбранной Вами модели. Реализуйте модульные тесты для сервиса. Авторизацию клиента оставьте такой же, как в примере.

4. Добейтесь работоспособности сервиса, продемонстрируйте корректную обработку запросов преподавателю.

5. Создайте свой репозиторий на `GitHub` с разработанным проектом. Реализуйте CI-конвейер в `GitHub Actions` для тестирования сервиса, убедитесь в его работоспособности.

6. Оформите первую главу пояснительной записки к **курсовой работе**, описав в ней следующие моменты:

* Постановку задачи.
* Краткое описание разработанной или выбранной модели со ссылкой на ее расположение (не более пары страниц), для решения какой задачи она используется.
* Описание API сервиса (какие ресурсы поддерживает сервис, какие методы к нему применяются, какие данные и какого формата передаются, какие коды ответов сервис возвращает в различных ситуациях, как сервер реагирует на ошибочные запросы, и т.д.).
* Описание проектирования и реализации сервиса с использованием выбранного фреймворка, фрагменты кода наиболее важных функций с их описанием.
* Тестирование работоспособности сервиса и CI (содержание запросов, содержание 
  ответов, демонстрация корректной обработки сервисом различных сценариев, возникающих в процессе его использования (в том числе, ошибочных), скрины с результатами тестирования и их пояснением).
* Выводы по главе с анализом полученных результатов.

#### Список вопросов к отчету работы

1. Жизненный цикл системы машинного обучения. Методология CRISP-DM.
2. Понятие инференса, виды инференса.
3. Понятие и назначение протокола HTTP. История и версии протокола.
4. Понятие ресурса в HTTP. Понятие URL (URI), его формат.
5. Этапы работы HTTP. Структура запросов и ответов.
6. MIME-типы, их назначение и характеристика.
7. Методы протокола HTTP. Безопасность, идемпотентность и кэшируемость.
8. Заголовки HHTP: общие, запросов, ответов, содержимого.
9. Коды ответов сервера и их интерпретация.
10. Управление кэшированием в HTTP.
11. Понятие веб-сервисов. Архитектура веб-сервисов.
12. Архитектурный стиль REST, принципы проектирования RESTful-сервисов.
13. Реализация CRUD в REST.
14. Модели зрелости REST по Ричардсону.
15. Понятие микросервисной архитектуры. Её преимущества и недостатки. Сравнение с монолитным приложением.
16. Стандарт OpenAPI для описания REST API. Основные особенности. Примеры фреймворков, поддерживающих OpenAPI.

#### Список литературы для подготовки к отчету

1. Хапке, Х. Разработка конвейеров машинного обучения : руководство / Х. Хапке, К. Нельсон ; перевод с английского Н. Б. Желновой. — Москва : ДМК Пресс, 2021. — 346 с. — ISBN 978-5-97060-886-9. — Текст : электронный // Лань : электронно-библиотечная система. — URL: https://e.lanbook.com/book/241088 (дата обращения: 27.02.2023).

2. Поллард, Б. HTTP/2 в действии : руководство / Б. Поллард ; перевод с английского П. М. Бомбаковой. — Москва : ДМК Пресс, 2021. — 424 с. — ISBN 978-5-97060-925-5. — Текст : электронный // Лань : электронно-библиотечная система. — URL: https://e.lanbook.com/book/241037 (дата обращения: 27.02.2023).

3. Fielding, R.T. Architectural Styles and the Design of Network-based Software Architectures: dissertation ... doctor of philosophy in Information and Computer Science / Fielding, R.T. - Irvine, 2000. - URL: https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm (дата обращения: 27.02.2023).

4. OpenAPI Initiative [Электронный ресурс] : документация по спецификации OpenAPI. – [2023]. – Режим доступа : https://www.openapis.org/ (дата обращения: 27.02.2023).

5. FastAPI [Электронный ресурс] : документация по фреймворку FastAPI. – [2023]. – Режим доступа : https://fastapi.tiangolo.com/ (дата обращения: 27.02.2023).