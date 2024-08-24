# Платформа ценообразования Авито

![Spring](https://img.shields.io/badge/spring-%236DB33F.svg?style=for-the-badge&logo=spring&logoColor=white)
![React](https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB)

Сервис предоставляет админ панель, которая предназначена для аналитиков и позволяет устанавливать, изменять и удалять цены для узлов дерева локаций и категорий.

## Структура сервиса

- **frontend** - Админ-панель
- **backend** - Бекенд, предоставляющий REST API для админ-панели

## Используемые технологии

- Frontend: **React, Mobx**
- Backend: **Spring**
- База данных: **PostgreSQL**
- Веб-сервер: **Caddy**
- Инструмент развёртывания: **Docker**

## Алгоритм поиска цен

Реализация сервиса отдачи цен приведена в файле [PriceService.java](https://github.com/dmtr636/it-purple-hack-avito/blob/master/backend/src/main/java/app/price/PriceService.java). В исходном коде приведены поясняющие комментарии.

Для нахождения цены используется алгоритм поиск вверх по дереву. Применена оптимизация, позволяющая с помощью одного запроса узнать о наличии цены в матрице. Таким образом значительно сокращается время проверки Discount матриц. 

Было проведено нагрузочное тестирование с использованием встроенных средств Postman

Получены следующие значения:
- Среднее время ответа: 11 мс.
- Максимальное время ответа (99 перцентиль): 115 мс.

Данные берутся из Discount матрицы, содержащей только одну строчку с ROOT категорией и локацией. Следовательно данный запрос формирует наибольшую нагрузку на сервис, т.к. для получения цены требуется максимальное количество итераций алгоритма.

Преимуществами данного алгоритма являются:
- Быстрая скорость обновления активных матриц в сторадже. За счёт хранения всей информации в одной таблице переключенение матриц происходит практически мгновенно. Отсутствует “моргание” цен.
- Лёгкость масштабирования и администрирования сервиса

Недостатком используемого подхода является большая нагрузка на базу и трудозатраты по её администрированию, кластеризации и репликации. 

