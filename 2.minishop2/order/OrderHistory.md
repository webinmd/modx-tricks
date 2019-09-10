# История заказов в minishop2 

#### Выводим историю заказов парой своих сниппетов и pdoResources


##### Создать страницу с вызовом:


```
{'pdoResources' | snippet: [
    'parents' => 0,
    'class' => 'msOrder',
    'limit' => 0,
    'depth' => 2,
    'sortdir' => 'DESC', 
    'sortby' => 'id',
    'tpl' => '@FILE chunks/orders/tplOrder_row.tpl',
    'where' => [
        'user_id' => $_modx->user.id
    ]
]}

```

Если необходима пагинация, то заменяем pdoResources на pdoPage 


##### Чанк tplOrder_row.tpl
Оформление приводится для примера, заменяйте на своё по необходимости

``` html
<div class="order-row">
    <div class="order-row-head">
        Заказ номер №{$num} 
        от {$createdon | date_format : '%d.%m.%Y'} 
        на сумму - {$cost | number : 0 : '.' : ' '}  руб
    </div>
    <div class="order-row-status">
        Статус заказа - 
        {'@FILE snippets/returnStatusCaption.snippet.php' | snippet : ['id' => $status]}
    </div>
    <div class="cart">
        <div class="cart__row cart__header">
            <div class="cart__td">Товар</div>
            <div class="cart__td">Цена</div>
            <div class="cart__td">Количество</div>
            <div class="cart__td">Сумма</div>
            <div class="cart__td">Вес брутто</div>
        </div><!-- /.cart__row -->

        {'@FILE snippets/getProductsOrder.snippet.php' | snippet : [
            'id' => $id,
            'tpl' => '@FILE chunks/orders/productRow.tpl'
        ]}
    </div><!-- /.cart -->
</div>
```


##### Сниппет returnStatusCaption.snippet.php
Получение заголовка текущего статуса заказа


```php
<?php

if(!$id) return;

$status = $modx->getObject('msOrderStatus', $id);
return  $status->get('name');
```


##### Сниппет getProductsOrder.snippet.php
Получение товаров текущего заказа

```php
<?php

if(!$id) return;

$q = $modx->newQuery('msOrderProduct', array('order_id' => $id));
$q->leftjoin('msProductData', 'product', 'product.id = msOrderProduct.product_id');

$q->limit(1000);
// если не нужны все поля, то можно раскомментировать строку ниже
//$q->select('product_id, name, price,count,cost,weight, options, product.image, product.article');
$q->select(array(
    'msOrderProduct.*',
    'product.*'
));

$q->prepare();
$q->stmt->execute();
$res = $q->stmt->fetchAll(PDO::FETCH_ASSOC);

$pdoTools = $modx->getService('pdoTools');
$output = '';

foreach($res as $v){
    $output .=  $pdoTools->getChunk($tpl, $v);
}

return $output;
```


##### Чанк productRow.tpl
Пример оформления ряда с товаром в заказе


``` html
<div class="cart__row">
    <div class="cart__td">{$name}</div>
    <div class="cart__td">{$price}</div>
    <div class="cart__td">{$count}</div>
    <div class="cart__td">{$cost}</div>
    <div class="cart__td">{$weight}</div>
</div><!-- /.cart__row -->
```