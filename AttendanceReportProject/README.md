# Отчет о посещаемости / Attendance report

## :dart:Цели
- Определение KPI (общее число рассматриваемых дней, рабочих дней за рассматриваемый период, посещаемость, процентное
соотношение посещенных дней к общему числу рабочих дней, количество дней в месяце, количество рабочих дней в месяце)
- Статистика по рабочим/праздничным/больничным дням

## :small_orange_diamond:Ключевые метрики
- Процентное значение посещаемости

## :small_orange_diamond:Источники данных
| Данные          | Формат |
|-----------------|--------|
|  emp_attendance_data  | Excel  |

## :small_orange_diamond:Используемые технологии
- Power Query для ETL
- DAX-формулы для мер

## :small_orange_diamond:Структура дашборда

**Используемые меры:**

Мера для определения общего числа рассматриваемых дней: `Total Days = COUNTROWS('Attendance Data')`

Мера для определения посещаемости: `Total Attendance = CALCULATE([Total Days], 'Status'[Status] in {"Remote", "Present", "On site", "On call"})`

Мера для определения общего числа рабочих дней календаря: `Total Working Days = CALCULATE([Total Days], 'Calendar'[Day type] = 1)`

Мера для определения процентного соотношения посещаемости к числу рабочих дней: `Attendance Pct = DIVIDE([Total Attendance], [Total Working Days])`

Мера для отображения числа дней в выбранном месяце: `Total Days in Month = COUNTROWS('Calendar')`

Мера для определения числа рабочих дней в месяце: `Total Work Days in Month = CALCULATE([Total Days in Month], 'Calendar'[Day type] = 1)`

Мера для отображения чисел в календаре: `Current Day = SELECTEDVALUE('Calendar'[Day])`

Меры для заполнения таблицы по сотрудникам (4 на каждый тип статуса по аналогии): `Vacation = CALCULATE(COUNTROWS('Attendance Data'), 'Status'[Status] = "Vacation")`
___
### Главная страница

![Image](https://github.com/user-attachments/assets/59bf69ca-ed8f-4da6-8e15-0748d2fd5513)

1) Заголовок, отражающий процетное значение посещаемости в выбранном месяце.  
Мера для расчета:  
```
Report Title = 
    var att_pct_disp = FORMAT([Attendance Pct], "0%")
    var sel_month = SELECTEDVALUE('Calendar'[Month Name])
return
    att_pct_disp & " Attendance in " &sel_month
```

2) Таблица с именами сотрудников и статистикой по рабочим дням

3) Календарь содержащий процент посещаемости в каждый день.
Для календаря использован SVG. Код заполнения ячеек календаря:
```
SVG for Calendar = 
    var cd = [Current Day]
    var att_pct = [Attendance Pct]
    var arr_pct_disp = FORMAT(att_pct, "0%")
    var day_type = SELECTEDVALUE('Calendar'[Day type])
    var day_type_color = SWITCH(true(), day_type = 1, "#B0FF9C", day_type = 2, "#FF6284", "#656079")
RETURN
"data:image/svg+xml;utf8, " &
"<svg width='100' height='100' viewBox='0 0 100 100' fill='none' xmlns='http://www.w3.org/2000/svg'>
<rect width='100' height='100' rx='10' fill='#51A3BA'/>
<text fill='white' xml:space='preserve' style='white-space: pre' font-family='Roboto' font-size='30' font-weight='600' letter-spacing='0em'><tspan x='58' y='31.2273'>"& cd &"</tspan></text>
<circle cx='13.5' cy='84.5' r='7.5' fill='"& day_type_color &"'/>
<text fill='white' xml:space='preserve' style='white-space: pre' font-family='Roboto' font-size='23' letter-spacing='0em'><tspan x='25' y='90.5455'>"&arr_pct_disp &"</tspan></text>
</svg>"
```
4) Фильтр для фильтрации по месяцам

5) Список заданных в условии KPI
___
## :small_orange_diamond:Установка и запуск  
Открыть файл `AttendanceReport.pbix` в Power BI Desktop.

Настроить подключения к данным из файла `emp_attendance_data.xlsx`.
