# Норникель (хакатон: грязные делешки) - Автоматическое Обнаружение Водной Поверхности

Этот проект реализует систему автоматического обнаружения водной поверхности с использованием YOLOv8 для семантической сегментации. Система предназначена для идентификации и сегментации водных поверхностей на аэрофотоснимках.

## Содержание
- [Структура проекта](#структура-проекта)
- [Архитектура нейронной сети](#архитектура-нейронной-сети)
- [Установка](#установка)
- [Датасет](#датасет)
- [Обучение](#обучение)
- [Инференс](#инференс)
- [Файлы моделей](#файлы-моделей)
- [Результаты](#результаты)

## Структура проекта

```
AWSD/
├── datasets/               # Директория с исходным датасетом
│   ├── images/            # Входные изображения
│   └── masks/             # Маски истинных значений
├── yolo_dataset/          # Обработанный датасет в формате YOLO
│   ├── train/             # Данные для обучения
│   └── val/               # Данные для валидации
├── runs/                  # Результаты обучения и логи
├── train.py              # Основной скрипт обучения
├── train2.py             # Альтернативный скрипт обучения с модификациями
├── infer_by_test.py      # Скрипт инференса для тестирования
├── baseline.pt           # Веса обученной модели
├── yolov8n-seg.pt        # Модель YOLOv8 nano для сегментации
└── requirements.txt      # Зависимости проекта
```

## Архитектура нейронной сети

В проекте используется архитектура YOLOv8 (You Only Look Once version 8), оптимизированная для задачи семантической сегментации.

### Основные компоненты архитектуры:

1. **Backbone**: 
   - CSPDarknet как основа для извлечения признаков
   - Модифицированная архитектура с Cross-Stage Partial Networks
   - Использование C2f блоков для улучшенного извлечения признаков

2. **Neck**:
   - PAN (Path Aggregation Network) для эффективного объединения признаков
   - Восходящий и нисходящий пути для улучшения распространения информации
   - Множественные уровни масштабирования для обработки объектов разного размера

3. **Head**:
   - Специализированная голова для сегментации
   - Декодер для генерации масок высокого разрешения
   - Использование множественных уровней предсказания

### Особенности реализации:

- **Архитектурные улучшения**:
  - Использование anchor-free подхода
  - Оптимизированные C2f блоки
  - Улучшенная схема аугментации данных

- **Модификации для сегментации**:
  - Дополнительные сверточные слои для точной сегментации
  - Маски высокого разрешения для точного определения границ
  - Оптимизированный декодер для работы с водными поверхностями

- **Оптимизации производительности**:
  - Использование батч-нормализации
  - Оптимизированная структура сети для баланса скорости и точности
  - Эффективное использование вычислительных ресурсов

### Параметры модели:

- Входное разрешение: 640x640 пикселей
- Количество параметров: ~3.2M (для nano версии)
- FPS: ~30 на GPU (зависит от hardware)

## Установка

1. Клонируйте репозиторий
2. Установите необходимые зависимости:
```bash
pip install -r requirements.txt
```

## Датасет

Датасет организован следующим образом:
- `datasets/images/`: Содержит оригинальные изображения в формате JPG
- `datasets/masks/`: Содержит соответствующие файлы масок в формате PNG
- `yolo_dataset/`: Содержит обработанный датасет в формате YOLO, автоматически создается во время обучения

Датасет автоматически разделяется на обучающую (80%) и валидационную (20%) выборки во время подготовки.

## Обучение

Проект включает два скрипта обучения:

### train.py
Основной скрипт обучения, который включает:
- Подготовку датасета (функция `prepare_dataset()`)
- Конфигурацию обучения модели
- Инициализацию и обучение модели YOLO

Для запуска обучения:
```bash
python train.py
```

### train2.py
Альтернативный скрипт обучения с измененными параметрами и конфигурациями для экспериментов.

## Инференс

Скрипт `infer_by_test.py` выполняет инференс модели на тестовых изображениях. Он:
- Загружает обученную модель
- Обрабатывает входные изображения
- Генерирует маски сегментации
- Сохраняет результаты в формате JSON

Использование:
```bash
python infer_by_test.py <путь_к_датасету> <путь_к_выходному_файлу>
```

## Файлы моделей

- `baseline.pt`: Основные веса обученной модели
- `yolov8n-seg.pt`: Базовая модель YOLOv8 nano для сегментации
- Дополнительные чекпоинты модели сохраняются в директории `runs/` во время обучения

## Результаты

Результаты обучения и метрики производительности модели хранятся в:
- `runs/`: Содержит логи обучения, метрики и визуализации
- `submit.json`: Содержит результаты инференса в требуемом формате

## Зависимости

Все необходимые пакеты перечислены в `requirements.txt`. Основные зависимости включают:
- ultralytics (YOLOv8)
- OpenCV
- NumPy
- PyYAML

Для подробной информации о версиях см. `requirements.txt`.