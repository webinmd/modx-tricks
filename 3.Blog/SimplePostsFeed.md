# Пример создания простой ленты новостей с пагинацией 

#### Использованы: pdoTools: pdoPage + pdoResources

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


##### Пример вызова на fenom 

```html
{$_modx->runSnippet('!pdoPage', [
'limit' => 10,
'element' => 'pdoResources', 
'tpl' => 'tpl.articleItem',
'includeTVs' => 'img',
'tvPrefix' => '',
'processTVs' => 'img',
'sortby' => '{ "menuindex":"ASC" }'
])} 

<div class="clr"></div>

{$_modx->getPlaceholder('page.nav')}

```



##### Пример чанков тегами fenom 

```html
{var $image = $_modx->runSnippet('phpthumbof', ['input' => $image, 'options' => '&w=380&h=200&zc=1'])}

<div class="news-item">
	<div class="row">
		<div class="col-md-4">
			<a href="{$uri}" class="news-item_img">
				<img src="{$image}" alt="{$pagetitle}">
			</a>
		</div>
		<div class="col-md-8">
			<div class="dt">{$publishedon | date_format: "%d.%m.%Y"}</div>
			<a href="{$uri}" class="news-item_title">{$pagetitle}</a>
			<div class="news-item_intro">{$introtext|notags|truncate:200:" ..."}</div>
		</div>
	</div>
</div> 

```