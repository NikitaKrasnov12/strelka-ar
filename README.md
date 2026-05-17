# Стрелка AR — Веб-приложение с дополненной реальностью

Визуализация исторического облика территории «Стрелка» советского периода (1917-1991), Нижний Новгород.

**Автор:** Краснов Н.М., группа 22ИТД2, НГТУ им. Р.Е. Алексеева, 2026

---

## Структура проекта

```
strelka-ar/
├── index.html              Лендинг (начальная страница)
├── brochure.html           Брошюра (4 главы + карты + AR-ссылки)
├── css/
│   ├── style.css           Основные стили (708 строк)
│   └── landing.css         Стили лендинга
├── pages/                  7 AR-страниц
│   ├── ganz_16.html
│   ├── takraf_albatros.html
│   ├── ganz_5.html
│   ├── sobor.html
│   ├── pakgauz.html
│   ├── water_station.html
│   └── tp_rp.html
├── models/                 3D-модели (.glb + .usdz)
├── images/                 Фотографии
└── README.md
```

---

## Быстрый старт

1. Скачайте проект
2. Положите 3D-модели в `models/`, фото в `images/`
3. Получите API-ключ Яндекс.Карт (см. ниже)
4. Замените `YOUR_API_KEY` в `brochure.html`
5. Запустите локальный сервер:

```bash
cd strelka-ar
python -m http.server 8000
```

Откройте `http://localhost:8000`

---

## Получение API-ключа Яндекс.Карт

Карты в брошюре работают через Яндекс.Карты API 2.1. Для работы нужен бесплатный API-ключ:

1. Перейдите на https://developer.tech.yandex.ru/
2. Войдите через Яндекс-аккаунт
3. Нажмите «Подключить API» → «JavaScript API и HTTP Геокодер»
4. Заполните название проекта (например, «Стрелка AR»)
5. Скопируйте полученный ключ
6. В файле `brochure.html` найдите строку:
   ```
   ?apikey=YOUR_API_KEY&lang=ru_RU
   ```
   и замените `YOUR_API_KEY` на ваш ключ

Ключ бесплатный для некоммерческого использования (до 25 000 запросов в сутки).

---

## 3D-карты территории

В брошюре есть два варианта карт (переключатель «Карта / 3D-вид»):

### Вариант 1: Яндекс.Карты (2D)
Стандартная интерактивная карта с метками объектов.

### Вариант 2: 3D-карта территории
CSS-перспектива с флажками-маркерами. Показывает только территорию Стрелки.

**Как заменить 3D-карту на свою модель территории:**

Текущая 3D-карта — это CSS-фигуры (наклонная плоскость + река). Если нужна реальная 3D-модель территории:

1. Создайте модель в Blender:
   - Импортируйте рельеф (DEM-данные с SRTM или OpenTopography)
   - Создайте mesh территории Стрелки
   - Добавьте текстуру (спутниковый снимок или стилизованная карта)
   - Разместите маркеры как отдельные объекты

2. Экспортируйте:
   - Формат: `.glb` (File → Export → glTF 2.0, формат GLB)
   - Размер: до 5 МБ (для быстрой загрузки в браузере)

3. Замените CSS-карту на `<model-viewer>`:
   ```html
   <div id="map1-3d" class="map-3d">
       <model-viewer src="models/strelka_map.glb"
           camera-controls auto-rotate
           style="width:100%;height:100%">
       </model-viewer>
   </div>
   ```

**Форматы 3D-моделей:**
| Формат | Платформа | Как создать |
|--------|-----------|-------------|
| `.glb` | Android, ПК | Blender: File → Export → glTF 2.0 (.glb) |
| `.usdz` | iOS | Reality Converter (macOS) или online-конвертер |

---

## Деплой на GitHub Pages

### Шаг 1: Репозиторий
GitHub → New repository → `strelka-ar` → Public

### Шаг 2: Загрузка
```bash
cd strelka-ar
git init && git add . && git commit -m "init"
git branch -M main
git remote add origin https://github.com/USERNAME/strelka-ar.git
git push -u origin main
```

### Шаг 3: Включить Pages
Settings → Pages → Source: main branch → Save

Сайт: `https://USERNAME.github.io/strelka-ar/`

### Бесплатный домен
- [freedns.afraid.org](https://freedns.afraid.org) — бесплатные поддомены
- CNAME-запись: target = `USERNAME.github.io`
- GitHub: Settings → Pages → Custom domain → Enforce HTTPS

---

## Технологии

| Технология | Назначение |
|-----------|-----------|
| HTML5 / CSS3 | Верстка |
| Яндекс.Карты API 2.1 | 2D-карты |
| CSS 3D transforms | 3D-карта территории |
| model-viewer (Google) | 3D/AR |
| IntersectionObserver | Анимация при скролле |
