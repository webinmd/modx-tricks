# Пример создания простой ленты новостей с пагинацией 

#### Использованы: pdoTools - pdoPage + pdoResources

**Задача:**<br>
Создать простую ленту постов или новостей с пагинацией

```html
[[!pdoPage?
	&depth=`1`
	&sortby=`id` 
	&tpl=`tpl.articleItem`
	&includeTVs=`img`
	&tvPrefix=``
	&limit=`5` 
]] 

<div class="clr"></div>
[[!+page.nav]]

```