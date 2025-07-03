# Анализ листа ожидания амбулаторных и стационарных больных / Waiting list analysis

## :dart:Цели
1. Мониторинг текущего состояния листа ожидания пациентов
2. Анализ динамики очереди по стационарному (Inpatient) и амбулаторному лечению (Outpatient) по месяцам
3. Анализ по направлениям лечения и по возрастным категориям

## :small_orange_diamond:Ключевые метрики
- Среднее и медианное количество пациентов в листе ожидания
- Общее текущее количество пациентов в листе ожидания

## :small_orange_diamond:Источники данных
| Данные          | Формат | Охват    |
|-----------------|--------|----------------|
| Inpatient       | CSV    | 2018 - 2021 гг. |
| Outpatient      | CSV    | 2018 - 2021 гг. |

## :small_orange_diamond:Используемые технологии
- Power Query для ETL
- DAX-формулы для мер

## :small_orange_diamond:Структура дашборда

**Используемые вспомогательные меры:**

Мера для анализа медианных значений: `Median Wait List = MEDIAN(All_Data[Total])`

Мера для анализа средних значений: `Average Wait List = AVERAGE(All_Data[Total])`

Ключевая мера **Avg/Med Wait List** для переключения между средними и медианными значениями с помощью слайсера и вспомогательной таблицы Calculation Method:
```Avg/Med Wait List = SWITCH(VALUES('Calculation Method'[Calc Method]), "Average", [Average Wait List], "Median", [Median Wait List])```

Мера для динамического отображения заголовка выбранного метода анализа:  
```Dynamic Title = SWITCH(VALUES('Calculation Method'[Calc Method]), "Average", "Key Indicators - Patient Wait List (Average)", "Median", "Key Indicators - Patient Wait List (Median)")```
___
### Главная страница. Сводка ключевых метрик

![Image](https://github.com/user-attachments/assets/7cc27494-866b-4372-af42-405d8c642364)

1) **Блок Total Wait List Comparison**  

**Latest Month Wait List** - Общее число пациентов за последний месяц   
```Latest Month Wait List = CALCULATE(SUM(All_Data[Total]), All_Data[Archive_Date] = MAX(All_Data[Archive_Date])) + 0```  

**PY Latest Month Wait List** - Число пациентов за аналогичный последнему месяц предыдущего года  
```PY Latest Month Wait List = CALCULATE(SUM(All_Data[Total]), All_Data[Archive_Date] = EDATE(MAX(All_Data[Archive_Date]), -12)) + 0```

2) **Кольцевая диаграмма Case Type Split** 
Распределение количества пациентов в зависимости от типа лечения (Case_Type). В качестве значений используется Avg/Med Wait List


3) **Столбчатая диаграмма с накоплением Time Band vs. Age Profile.** Распределение количества пациентов из Avg/Med Wait List по возрасту и времени пребывания в больнице


4) **Top 5 Specialty**. Топ 5 специализаций по количеству пациентов из Avg/Med Wait List  


5) **Monthly Trend Analysis - Day Case / Inpatient vs Outpatiens**. Графики для анализа динамики общего числа пациентов по типу лечения по месяцам

Контролы для задания фильтров по специализации, по типу лечения и дате, а также контролы для перехода между страницами.

Всплывающая подсказка по количеству пациентов по общим специализациям из графика по месячной тенденции (DrillDown).

![Image](https://github.com/user-attachments/assets/2420fd0b-0870-4c51-a7dd-eae249e2ad22)
___
### Страница с детализацией для глубокой аналитики 
Блок с контролами определения фильтров и таблица для подробного анализа

![Image](https://github.com/user-attachments/assets/a977814b-349f-41a0-b3cc-01cf81d98717)
___
## :small_orange_diamond:Установка и запуск  
Открыть файл `WaitingListAnalysis.pbix` в Power BI Desktop

Настроить подключения к данным из папок `Inpatient`, `Outpatient` и к файлу `Mapping_Specialty.csv`
