# MODX CKEditor video 

### Вставка видео плеера в редактор CKEditor 

**Задача:**
Вставить видео с сервера в контент сайта
 
**Решение:**

* Скачать плагин по [ссылке](https://ckeditor.com/cke4/addon/video), либо найти любой понравившийся
* Загрузить плагин в папку **/manager/assets/components/ckeditor/ckeditor/plugins**
* В системных настройках для ключа **ckeditor.extra_plugins** прописать значение **video**

В редакторе появится кнопка 
[![](https://file.modx.pro/files/6/7/5/675d13d3784b4460d767069aea0e5e73s.jpg)](https://file.modx.pro/files/6/7/5/675d13d3784b4460d767069aea0e5e73.png)


При вставке видео может выскочить ошибка: 
**URL is invalid or video is not playable.**

Просто поставьте слеш перед путем к файлу, либо настройте правильно источник файлов
[![](https://file.modx.pro/files/8/b/9/8b9ce9a6f8efa82d717c6fd4a09a5fe0s.jpg)](https://file.modx.pro/files/8/b/9/8b9ce9a6f8efa82d717c6fd4a09a5fe0.png)