# Как добавить товары в конкретный заказ пользователя

Запускать в компоненте консоли внутри MODX 
Параметры изменять по своему усмотрению

```php
// в какой заказ добавить все товары
$order_id = 666;

// условия выборки товаров
$output = $modx->runSnippet('msProducts', array(
    'parents' => '7',
    'depth' => '5',
    'returnIds' => '1',
    'limit' => '0', 
    'where' => '{"published":"1"}',
    'templates' => '5'
));

$products = explode(',',$output);

foreach($products as $product_id){    
    echo $product_id."<br>";    
    if (!$orderProduct = $modx->getObject('msOrderProduct', array('order_id' => $order_id, 'product_id' => $product_id))) {        
        $orderProduct = $modx->newObject('msOrderProduct', array('order_id' => $order_id, 'product_id' => $product_id));
        $orderProduct->save();
    }  
}
```
