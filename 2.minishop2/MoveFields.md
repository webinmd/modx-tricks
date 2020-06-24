# Перемещаем TV и родные поля miniShop2

#### Задача 1:
**Переместить поле Цена в первую вкладку ресурса и скрыть вкладку Свойства товара (так как все поля кроме цены были выключены)**

![Результат](/assets/images/2020-06-24_20-21-31.png) 

Создать плагин 

```php
<?php
$eventName = $modx->event->name;

switch($eventName) { 
    case 'OnDocFormPrerender':
        
        $modx->regClientStartupHTMLBlock("<script>
        
        Ext.onReady(function () {

        	// что перемещаем
            var price = Ext.getCmp('modx-resource-price');
			
			// куда перемещаем
            var panel = Ext.getCmp('modx-resource-main-left');
            
            panel.insert(1,price);
            panel.doLayout();
            
            // что скрываем
            var tab = Ext.select('#minishop2-product-tabs .x-tab-strip li').item(2);
            tab.hide(); 
        });
        </script>");
        
        
        break;
        
}
```


Для версии miniShop 2.5.0. еще не было уникального id для каждой вкладки, поэтому приходится перебором получать нужный нам таб, за это отвечает вот этот кусок кода. Для версии 2.5.1 этот код можно будет переписать.

```js
Ext.select('#minishop2-product-tabs .x-tab-strip li').item(2);
```


#### Задача 2:
**Переместить TV поле в вкладку Свойства товара (вкладка товара).**

Для версии 2.5.1 можно для этого воспользоваться настройкой форм (встроенный функционал). Для более ранних версий, в которых у вкладок и областей нет уникального постоянного ID, можно воспользоваться следующим кодом. 

Плагин:
```php
<?php
$eventName = $modx->event->name;

switch($eventName) { 
    case 'OnDocFormPrerender':
        
        $modx->regClientStartupHTMLBlock("<script>
        
        Ext.onReady(function () {
            // получаем панель из вкладки в которой находится поле цена
            var panelMinishop = Ext.getCmp('modx-resource-price');
            var parent = panelMinishop.findParentByType('panel');
            
            // 8 - указать id нужного TV поля
            MODx.moveTV(['tv8'], parent.id);
        });
        </script>");
         
        break;
}
```