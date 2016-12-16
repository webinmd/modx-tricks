# Добавить свои поля в заказ компонента minishop2 в панели администратора

**Источник:**
[modx.pro](https://modx.pro/solutions/10040-add-your-fields-in-the-order-form/)

### Без изменения исходников

![Order Fields](/assets/images/order_1.jpg)


**Задача:**
Сохранять дополнительные поля в панели администратора при оформлении заказа 

**Шаги** 
1. Добавить необходимые поля, для примера взяты тип плательщика, название организации и инн.
2. Добавить плагин срабатывающий при сохранении заказа и при подключении js минишопа в админке.
3. Добавить настройки полей в minishop2
4. Добавить записи записи словарей.


**1.** В форму оформления заказа добавляем нужные поля:
```html
<label><input type="radio" name="extfld_type" value="Юридическое лицо">Юридическое лицо</label>
<label><input type="radio" name="extfld_type" value="Физическое лицо">Физическое лицо</label>
<label><input type="text" name="extfld_org">Название организации</label>
<label><input type="text" name="extfld_inn">ИНН</label>Все поля с префиксом extfld_ попадут в заказ.
```

**2.** Создаем плагин msExtraAddressFields и вешаем его на события msOnBeforeCreateOrder и msOnManagerCustomCssJs. Плагин сохраняет нужные поля при оформлении заказа в json в поле properties таблицы modx_ms2_order_addresses, а так же подключает нужный js в админке.
```php
<?php
switch ($modx->event->name) {
    case 'msOnBeforeCreateOrder':
        $address = $msOrder->getOne('Address');
        $properties = array();
        foreach ($_POST as $key => $value){
            if (strpos($key,'extfld_') !== false){
                $properties[$key] = htmlentities($value,ENT_COMPAT | ENT_HTML401,'UTF-8');
            }
        }
        if (count($properties) > 0){
            $address->set('properties', json_encode($properties));    
        }
    break;
    
    case 'msOnManagerCustomCssJs':
        if ($page != 'orders') return;
	$modx->controller->addHtml("
            <script type='text/javascript'>
                Ext.ComponentMgr.onAvailable('minishop2-window-order-update', function(){
                	if (miniShop2.config['order_address_fields'].in_array('properties')){
                		if (this.record.addr_properties){
                		    var key;
                			for (key in this.record.addr_properties) {
                				this.fields.items[2].items.push(
                					{
                						xtype: 'displayfield',
                						name: 'addr_properties_'+key,
                						fieldLabel: _('ms2_properties_'+key),
                						anchor: '100%',
                						style: 'border:1px solid #efefef;width:95%;padding:5px;',
                						html: this.record.addr_properties[key]
                					}
                				);
                			}
                		}		
                	}
                });                
            </script>");
    break;
}
```

**3.** Добавляем вывод поля properties: 
Заходим в системные настройки, там выбираем minishop2, блок Заказы.
Добавляем к значению «Поля адреса доставки» properties.

**4.** Добавляем записи словарей:
Заходим в Управление словарями, выбираем пространство имен minishop2, тема — manager, язык — ru.
Создаем новые записи с именами вида: ms2_properties_имя_вашего_поля (в данном случае ms2_properties_extfld_type, ms2_properties_extfld_org, ms2_properties_extfld_inn), значения — названия полей, которые будут отображаться в админке. 

Поля не редактируемые, только выводят отправленные покупателем данные

Результат 
![Order Fields](/assets/images/order_1.jpg)