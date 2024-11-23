1. **Сбор интервалов**:
   - Создаем список интервалов, где каждый интервал представлен как структура `{ начало_интервала, конец_интервала }`.
   - Пример: `intervals = [{ начало: 65:45:34.13, конец: 65:45:34.17 }, { начало: 65:45:34.20, конец: 65:45:34.30 }, ...]`.

2. **Агрегация интервалов**:
   - Устанавливаем максимальное расстояние между интервалами `max_gap` (например, 10 секунд).
   - Создаем пустой список для агрегированных интервалов `aggregated_intervals = []`.
   - Сортируем `intervals` по `начало_интервала`.
   - Инициализируем переменную `current_interval` как первый интервал из отсортированного списка.
   - Для каждого последующего интервала:
     - Если `начало_интервала` текущего интервала минус `конец_интервала` `current_interval` меньше или равно `max_gap`, то:
       - Обновляем `конец_интервала` `current_interval` на `конец_интервала` текущего интервала.
     - Иначе:
       - Добавляем `current_interval` в `aggregated_intervals`.
       - Устанавливаем `current_interval` как текущий интервал.
   - После завершения цикла добавляем последний `current_interval` в `aggregated_intervals`.

3. **Генерация меток**:
   - Создаем пустой список для меток `labels = []`.
   - Для каждого человека:
     - Устанавливаем первую метку как `начало_интервала` первого интервала.
     - Устанавливаем переменную `current_time` как `начало_интервала` первого интервала.
     - Пока `current_time` меньше `конец_интервала` последнего интервала:
       - Увеличиваем `current_time` на 5 минут.
       - Проверяем, попадает ли `current_time` в пределы какого-либо из интервалов:
         - Если да, добавляем `current_time` в `labels`.
         - Если нет, ищем ближайший интервал, начало которого больше `current_time`, и добавляем его `начало_интервала` в `labels`.
       - Если более поздних интервалов нет, прерываем поиск.

4. **Вывод результатов**:
   - Возвращаем `aggregated_intervals` и `labels`.

### Пример кода на Python

```python
from datetime import datetime, timedelta

def aggregate_intervals(intervals, max_gap):
    aggregated_intervals = []
    intervals.sort(key=lambda x: x['начало'])
    
    current_interval = intervals[0]
    
    for interval in intervals[1:]:
        if (interval['начало'] - current_interval['конец']).total_seconds() <= max_gap:
            current_interval['конец'] = max(current_interval['конец'], interval['конец'])
        else:
            aggregated_intervals.append(current_interval)
            current_interval = interval
            
    aggregated_intervals.append(current_interval)
    return aggregated_intervals

def generate_labels(aggregated_intervals, interval_duration):
    labels = []
    
    for interval in aggregated_intervals:
        first_label = interval['начало']
        labels.append(first_label)
        
        current_time = first_label
        
        while current_time < interval['конец']:
            current_time += interval_duration
            
            found = False
            for agg_interval in aggregated_intervals:
                if agg_interval['начало'] <= current_time <= agg_interval['конец']:
                    labels.append(current_time)
                    found = True
                    break
            
            if not found:
                next_interval = next((agg_interval for agg_interval in aggregated_intervals if agg_interval['начало'] > current_time), None)
                if next_interval:
                    labels.append(next_interval['начало'])
                else:
                    break
    
    return labels
```

# Связанные статьи: 
[[Описание алгоритма от саши]]
