# SystemIntegration

## Интеграционный сервис для синхронизации запчастей (n8n + PostgreSQL)

Проект представляет собой интеграционное приложение на n8n для синхронизации данных о запасных частях между PostgreSQL базой данных и CMS API через Report API. Система обеспечивает экспорт актуальных данных в CSV-формате без заголовков и отправку в CMS для обработки отчетов.

**Описание системы:**
Система состоит из двух основных параллельных потоков, запускаемых вручную через Execute workflow:

Поток 1 (Синхронизация):
Execute workflow → Make spares inactive → Get data from CMS → Insert or update

Поток 2 (Экспорт):
Execute workflow → Select rows → Make CSV → Report

## Поток 1 — Синхронизация CMS → PostgreSQL
Последовательность узлов:

1. Make spares inactive — делает запчасти неактивными, перед обращением к CMS

2. Get data from CMS — берёт данные у CMS через HTTP-запрос к http://212.237.219.35:8080/students/27/cms/spares

3. Insert or update rows in a table — Новые данные добавляются с статусом 'active', старые не меняются (уже имеют статус 'inactive'), обновлённые данные меняют свой статус из 'inactive' в 'active'

## Поток 2 — Экспорт PostgreSQL → Report API
Последовательность узлов:
1. Select rows from a table — Берём все данные из БД

2. Make Proper CSV Format — js код форматирует данные под нужный csv формат для репорта (строки объединяются через ";", после каждой строки добавляем перенос строки "\n", csv формируется как текстовая строка)

3. Report — Отправляет POST-запрос на http://212.237.219.35:8080/students/27/report/csv содержащий в теле данные из csv файла.
