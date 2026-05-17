# Инструкция по 3D-картам территории Стрелка

## Содержание

1. Текущая реализация (CSS)
2. Замена на реальную 3D-модель территории
3. Создание модели в Blender
4. Форматы и экспорт
5. Интеграция в проект
6. Источники геоданных

---

## 1. Текущая реализация (CSS-перспектива)

Сейчас 3D-карта сделана средствами CSS без внешних файлов:

```
.map-3d
├── .map-3d__ground      — наклонная плоскость (rotateX 45°, песочный цвет)
│   └── .map-3d__river   — полоса реки (градиент голубого)
├── .map-3d__marker      — маркер-ссылка (<a href="pages/...">)
│   ├── .map-3d__pin     — треугольник-флажок (CSS border trick)
│   ├── .map-3d__pin-dot — точка основания
│   └── .map-3d__label   — подпись объекта
```

**Плюсы:** не нужны внешние файлы, мгновенная загрузка, работает офлайн.
**Минусы:** схематично, нет реального рельефа и текстуры.

Позиции маркеров задаются в процентах через inline-стили:
```html
<a href="pages/sobor.html" class="map-3d__marker" style="left:42%;bottom:55%">
```

---

## 2. Замена на реальную 3D-модель территории

Для замены CSS-карты на полноценную 3D-модель используется
тот же компонент `<model-viewer>`, что и для AR-объектов.

### Шаг 1: Подготовка модели

Нужна 3D-модель участка территории Стрелки размером примерно
800×600 метров, включающая:
- Рельеф поверхности (плоский, Стрелка на пойме)
- Контуры рек (Ока, Волга)
- Текстуру (спутниковый снимок или стилизованная карта)
- Опционально: упрощенные объемы зданий

### Шаг 2: Экспорт

| Формат | Для чего | Размер |
|--------|----------|--------|
| `.glb` | Браузер (model-viewer) | До 5 МБ |
| `.usdz` | iOS AR Quick Look | До 5 МБ |

### Шаг 3: Замена в HTML

В файле `brochure.html` найдите блок 3D-карты нужной главы:
```html
<div id="map1-3d" class="map-3d map-hidden">
    <div class="map-3d__ground">...</div>
    <a href="..." class="map-3d__marker" ...>...</a>
</div>
```

Замените на:
```html
<div id="map1-3d" class="map-hidden" style="width:100%;height:420px;">
    <model-viewer
        src="models/strelka_territory.glb"
        camera-controls
        auto-rotate
        rotation-per-second="10deg"
        camera-orbit="45deg 55deg 500m"
        min-camera-orbit="auto auto 200m"
        max-camera-orbit="auto auto 1000m"
        style="width:100%;height:100%;background:#c8dbe8;"
        alt="3D-карта территории Стрелка">
    </model-viewer>
</div>
```

Не забудьте добавить `<script>` model-viewer в `<head>` файла
brochure.html (сейчас он подключен только в pages/*.html):
```html
<script type="module" src="https://ajax.googleapis.com/ajax/libs/model-viewer/3.5.0/model-viewer.min.js"></script>
```

---

## 3. Создание модели территории в Blender

### 3.1. Установка Blender

Скачайте бесплатно: https://www.blender.org/download/
Версия 3.6 LTS или новее.

### 3.2. Создание рельефа

**Вариант А: Плоская поверхность (простой)**

1. Удалите куб по умолчанию (X)
2. Add → Mesh → Plane
3. В свойствах (N) задайте размер: Scale X=800, Y=600
4. Tab → Edit Mode → правый клик → Subdivide (3-4 раза)
5. Выделите вершины в зоне рек → переместите вниз (G, Z, -2)

**Вариант Б: Реальный рельеф (продвинутый)**

1. Скачайте DEM-данные:
   - SRTM: https://dwtkns.com/srtm30m/ (тайл N56E043)
   - Или OpenTopography: https://opentopography.org/
2. В Blender: Add → Mesh → Plane
3. Modifier → Displace → Texture → Image → загрузите DEM
4. Настройте Strength (сила смещения) — для Стрелки минимальная,
   территория почти плоская

### 3.3. Добавление текстуры

**Вариант А: Спутниковый снимок**

1. Скачайте снимок территории из Яндекс.Карт (режим «Спутник»)
   или Google Earth Pro (File → Save Image)
   Координаты: примерно 56.325-56.340 N, 43.955-43.980 E
2. В Blender: выделите plane → Material Properties → New
3. Base Color → Image Texture → загрузите снимок
4. UV-развертка: Tab (Edit Mode) → U → Project from View (Top)

**Вариант Б: Стилизованная карта**

1. Создайте изображение в Figma/Photoshop:
   - Песочный фон для суши
   - Голубой для рек
   - Серый для дорог
   - Зеленый для парков
2. Размер: 2048×1536 px (или 4096×3072 для HD)
3. Примените как текстуру (аналогично варианту А)

### 3.4. Контуры рек

1. Add → Curve → Bezier → нарисуйте русло Оки и Волги
2. В свойствах кривой: Geometry → Bevel Depth = 5-10
3. Convert to Mesh (Object → Convert → Mesh)
4. Назначьте голубой материал

### 3.5. Маркеры объектов (опционально)

Если хотите встроить маркеры прямо в модель:

1. Для каждого объекта: Add → Mesh → Cone (маленький)
2. Расположите в координатах объекта
3. Назначьте красный материал
4. Подпишите через Add → Text (3D-текст)

Однако проще оставить маркеры в HTML (как сейчас)
и наложить их поверх model-viewer через CSS position:absolute.

### 3.6. Оптимизация

Для быстрой загрузки в браузере:
- Полигонов: не более 50 000
- Текстуры: JPEG, не более 2048×2048
- Удалите невидимую геометрию
- Примените модификатор Decimate если нужно

---

## 4. Форматы и экспорт

### Экспорт GLB из Blender

1. File → Export → glTF 2.0 (.glb/.gltf)
2. Настройки:
   - Format: `glTF Binary (.glb)` — один файл с текстурами
   - Include: Selected Objects (если нужна часть сцены)
   - Mesh → Apply Modifiers: включить
   - Material → Images: JPEG Quality 85%
   - Animation: выключить (если нет анимации)
3. Сохраните как `strelka_territory.glb` в папку `models/`

### Экспорт USDZ (для iOS)

**Вариант А: Reality Converter (macOS)**
1. Скачайте: https://developer.apple.com/augmented-reality/tools/
2. Откройте .glb файл → File → Export → .usdz

**Вариант Б: Онлайн-конвертер**
- https://products.aspose.app/3d/conversion/glb-to-usdz
- Загрузите .glb → скачайте .usdz

**Вариант В: Из Blender (плагин)**
1. Установите плагин: https://github.com/nicolo-o/blender-usdz-exporter
2. File → Export → USDZ

---

## 5. Интеграция в проект

### Файловая структура после добавления

```
models/
├── strelka_territory.glb    — 3D-карта территории
├── ganz_16.glb              — модель крана Ganz
├── sobor.glb                — модель собора
└── ...
```

### Параметры model-viewer для карты территории

```html
<model-viewer
    src="models/strelka_territory.glb"
    camera-controls                    — вращение мышью
    auto-rotate                        — автовращение
    rotation-per-second="10deg"        — скорость вращения
    camera-orbit="45deg 55deg 500m"    — начальный ракурс
        — 45deg  = горизонтальный угол (азимут)
        — 55deg  = вертикальный угол (наклон сверху)
        — 500m   = расстояние от центра
    min-camera-orbit="auto auto 200m"  — минимальный зум
    max-camera-orbit="auto auto 1000m" — максимальный зум
    environment-image="neutral"        — нейтральное освещение
    shadow-intensity="0.5"             — тень под моделью
    style="width:100%;height:420px;">
</model-viewer>
```

### Наложение HTML-маркеров поверх model-viewer

Если маркеры не встроены в модель, их можно наложить через CSS:

```html
<div style="position:relative;width:100%;height:420px;">
    <model-viewer src="models/strelka_territory.glb"
        camera-controls auto-rotate
        style="width:100%;height:100%;position:absolute;top:0;left:0;">
    </model-viewer>
    <!-- Маркеры поверх модели -->
    <a href="pages/sobor.html" class="map-3d__marker"
       style="left:42%;bottom:55%;z-index:10;">
        <div class="map-3d__pin"></div>
        <div class="map-3d__label">Собор</div>
    </a>
</div>
```

Учтите: при вращении модели маркеры останутся на месте (они в HTML, не в 3D).
Для маркеров, привязанных к модели, используйте слоты model-viewer:

```html
<model-viewer src="models/strelka_territory.glb" camera-controls>
    <button slot="hotspot-sobor"
            data-position="0 0.1 0.5"
            data-normal="0 1 0"
            onclick="location.href='pages/sobor.html'">
        Собор
    </button>
</model-viewer>
```

---

## 6. Источники геоданных

### Рельеф (DEM)
- SRTM 30m: https://dwtkns.com/srtm30m/
- OpenTopography: https://opentopography.org/
- Copernicus DEM: https://spacedata.copernicus.eu/

### Спутниковые снимки
- Google Earth Pro (бесплатно): https://earth.google.com/
- Яндекс.Карты (режим «Спутник»): https://yandex.ru/maps/
- SAS.Planet (бесплатно): http://www.sasgis.org/

### Контуры зданий и дорог
- OpenStreetMap: https://www.openstreetmap.org/
- Экспорт: https://overpass-turbo.eu/
  Запрос для территории Стрелки:
  ```
  [out:json][timeout:30];
  (way["building"](56.325,43.955,56.340,43.980););
  out body; >; out skel qt;
  ```

### Исторические карты
- Ретро-карты НН: https://retromap.ru/
- Это Место (старые карты): https://www.etomesto.ru/

---

## Рекомендации

1. **Начните с CSS-варианта** (уже реализован) — он работает без файлов
2. **Для ВКР** достаточно CSS-карты + скриншоты из Blender в тексте работы
3. **Для продакшена** замените на GLB-модель с текстурой спутникового снимка
4. **Размер GLB** держите до 3-5 МБ для быстрой загрузки на мобильных
5. **Тестируйте** на реальном телефоне через `python -m http.server`
