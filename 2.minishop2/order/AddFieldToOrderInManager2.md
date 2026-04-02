#  Добавить свое поле в заказ компонента minishop2

## Описание как добавлять свои поля в msOrder MiniShop2 без изменения исходников

**Задача:**<br>
Сохранять дополнительное поле адреса при оформлении заказа (например: ИНН - inn)
 
**Шаги:**  <br>
1. создать поле в базе в таблице ms2_order_addresses 
2. в плагине на событие OnMODXInit подключить новое поле, на событие msOnManagerCustomCssJs вывести его в админ панель
3. Добавить в чанке оформления поле с name как в базе
4. 

--------------------------------------------


**1.** Создать поле в базе в таблице ms2_order_addresses 
Открываем phpmyadmin, находим нужную таблицу и сздаем там поле с типом varcghar и длиной 190 

**2.** Создаем плагин extendDB и вешаем его на события OnMODXInit и msOnManagerCustomCssJs.
Плагин сохраняет нужные поля при оформлении заказа в поле inn таблицы modx_ms2_order_addresses, а так же подключает нужный js в админке.


```php
<?php
switch ($modx->event->name) {
    case 'OnMODXInit':
      $modx->loadClass('msOrderAddress'); 
    	$modx->map['msOrderAddress']['fields']['inn'] = '';
    	$modx->map['msOrderAddress']['fieldMeta']['inn'] = array(
                'dbtype' => 'varchar',
                'phptype' => 'string',
                'precision' => 190,
                'null' => true,
                'default' => null,
	    );
    break;
    
    case 'msOnManagerCustomCssJs':
      if ($page != 'orders') return;
      $modx->controller->addHtml("
        <script type='text/javascript'>
          Ext.ComponentMgr.onAvailable('minishop2-window-order-update', function(page) {
            this.fields.items[2].items.push(
              {
                xtype: 'textfield',
                name: 'inn',
                fieldLabel: 'ИНН',
                anchor: '100%', 
                style: 'margin-bottom: 15px;',
                value: page.record.inn,
              }
            );
          });                
        </script>");
    break;

}
```

Если нужно добавить поле в другую таблицу, то не зыбыть загрузить нужный класс.
Например: 
```php
$modx->loadClass('msOrder'); 
``` 


**3.** В форму оформления заказа добавляем нужные поля:
```html
<label><input type="text" name="inn">ИНН</label>
```



**В письме использовать следующим образом:**<br>
Если используется Fenom то<br>
```html
{$address.inn}
``` 
 
