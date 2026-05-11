# Стрелка AR — Веб-приложение с дополненной реальностью

Визуализация исторического облика территории «Стрелка» советского периода (1917-1991), город Нижний Новгород.

**Автор:** Краснов Н.М., группа 22ИТД2, НГТУ им. Р.Е. Алексеева, 2026

---

## Принцип работы

QR-код (или кнопка на сайте) ведет на веб-страницу объекта. На странице — 3D-модель и кнопка «Смотреть в AR». При нажатии модель отображается поверх реального вида камеры телефона. Установка приложений не требуется.

---

## Структура проекта

```
strelka-ar/
├── index.html              Главная страница (брошюра с историей)
├── css/
│   └── style.css           Все стили проекта
├── pages/                  AR-страницы объектов
│   ├── ganz_16.html
│   ├── takraf_albatros.html
│   ├── ganz_5.html
│   ├── takraf_kondor.html
│   ├── sobor.html
│   ├── pakgauz_exhibition.html
│   ├── pakgauz_concert.html
│   ├── water_station.html
│   └── tp_rp.html
├── models/                 3D-модели (.glb и .usdz)
├── images/                 Фотографии
└── README.md
```

---

## Быстрый старт (локально)

1. Скачайте или клонируйте проект
2. Положите 3D-модели (`.glb`, `.usdz`) в папку `models/`
3. Положите фотографии в папку `images/`
4. Откройте `index.html` в браузере

Для корректной работы AR и model-viewer нужен HTTPS (веб-сервер). Локально можно запустить через Python:

```bash
cd strelka-ar
python -m http.server 8000
```

Затем открыть `http://localhost:8000` в браузере.

---

## Деплой на бесплатный домен (GitHub Pages)

### Шаг 1: Создать репозиторий

1. Зарегистрируйтесь на [github.com](https://github.com)
2. Нажмите «New repository»
3. Имя: `strelka-ar` (или любое)
4. Public, без README (он уже есть)

### Шаг 2: Загрузить файлы

```bash
cd strelka-ar
git init
git add .
git commit -m "initial commit"
git branch -M main
git remote add origin https://github.com/ВАШ_ЛОГИН/strelka-ar.git
git push -u origin main
```

Или загрузите файлы через веб-интерфейс GitHub (кнопка «Add file» > «Upload files»).

### Шаг 3: Включить GitHub Pages

1. Settings > Pages
2. Source: Deploy from a branch
3. Branch: main, папка: / (root)
4. Save

Через 1-2 минуты сайт будет доступен по адресу:
```
https://ВАШ_ЛОГИН.github.io/strelka-ar/
```

### Шаг 4: Привязать свой домен (необязательно)

Бесплатный домен можно получить на [freedns.afraid.org](https://freedns.afraid.org) или [freenom.com](https://freenom.com):

1. Зарегистрируйте домен (например, `strelka-ar.tk`)
2. В настройках DNS создайте CNAME-запись:
   - Host: `@` или `www`
   - Target: `ВАШ_ЛОГИН.github.io`
3. В GitHub: Settings > Pages > Custom domain: `strelka-ar.tk`
4. Поставьте галочку «Enforce HTTPS»

---

## 3D-модели

Модели должны быть в двух форматах:

| Формат | Для чего | Как создать |
|--------|----------|-------------|
| `.glb` | Android, десктоп | Blender: File > Export > glTF 2.0 (.glb) |
| `.usdz` | iOS (AR Quick Look) | Reality Converter (macOS) или online-конвертер |

Имена файлов должны совпадать с id объекта: `ganz_16.glb`, `sobor.glb` и т.д.

---

## Технологии

| Технология | Назначение |
|-----------|-----------|
| HTML5 / CSS3 | Верстка и стили |
| model-viewer (Google) | 3D/AR в браузере |
| CSS Float | Обтекание фото текстом |
| IntersectionObserver API | Анимация при прокрутке |
| GitHub Pages | Бесплатный хостинг |

---

## Объекты (9 штук)

1. Кран Ganz 16/27,5т (Венгрия, 1974)
2. Кран TAKRAF «Альбатрос» 16/32 (ГДР, 1976)
3. Кран Ganz 5/6т (Венгрия, 1970)
4. Кран TAKRAF «Кондор» 16/32/40 (ГДР, 1980)
5. Собор Александра Невского (1881)
6. Пакгауз — выставочный зал (1882)
7. Пакгауз — концертный зал (1882)
8. Ярмарочная фильтровальная станция (1909)
9. ТП-РП 2820 (1960-е)
