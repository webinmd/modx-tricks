# Цена доставки в завсимости от различных параметров


### Без изменения исходников (тестировалось на minishop2 2.4.10)
 
**Задача:**<br>
Динамически изменять цену на доставку в зависимости от суммы заказа или количества товаров в корзине

**Шаги** <br>
1. Расширить класс доставки <br>
2. Создать новый способ доставки 

Расширим класс msDeliveryHandler. Все обновленные данные берутся $order->ms2->cart->status(); и отсюда следует что нужно в этот массив данных дописать нужные нам данные.
Создаем в папке core/components/minishop2/custom/delivery файл msdeliverycourierhandler.class.php


**1.1** Зависимость цены доставки от количества товаров в корзине

```php
<?php
if (!class_exists('msDeliveryHandler')) {
    require_once dirname(dirname(dirname(__FILE__))) . '/model/minishop2/msdeliveryhandler.class.php';
}

class msDeliveryCourierHandler extends msDeliveryHandler implements msDeliveryInterface {

    public function getCost(msOrderInterface $order, msDelivery $delivery, $cost = 0.0) {

        $cart = $order->ms2->cart->status();
        $cart_count = $cart['total_count'];

        // зависимость цены доставки от количества товаров в корзине
        // кол-во => цена 
        $counts = array(
            '1' => 5,
            '15' => 10,
            '30' => 15,
            '50' => 20
        ); 

        $new_cost = $counts[0];

        foreach($counts as $k => $v){
            if( $cart_count >= $k ){
                $new_cost = $v;
            }
        } 

        return $cost + $new_cost; 
 
    }

}
```


**1.2** Зависимость цены доставки от общей суммы


```php
<?php
if (!class_exists('msDeliveryHandler')) {
    require_once dirname(dirname(dirname(__FILE__))) . '/model/minishop2/msdeliveryhandler.class.php';
}

class msDeliveryCourierHandler extends msDeliveryHandler implements msDeliveryInterface {

    public function getCost(msOrderInterface $order, msDelivery $delivery, $cost = 0.0) {

        $freedeliverysumm = 1000; // сумма от которой доставка станет бесплатной
        $cart = $order->ms2->cart->status();
        $cart_cost = $cart['total_cost']; 

        if($cart_cost > $freedeliverysumm){
            return $cost;
        }else{
            $delivery_cost = parent::getCost($order, $delivery, $cost);
            return $delivery_cost;
        }
 
    }

}
```


**После создания файла с классом, необходимо зарегестрировать новую службу в сервисах магазина**
Самый простой способ это установить компонент Console и выполните там следующий код: 

```php
if ($miniShop2 = $modx->getService('miniShop2')) {
    $miniShop2->addService('delivery', 'msdeliverycourierhandler',
        '{core_path}components/minishop2/custom/delivery/msdeliverycourierhandler.class.php'
    );
}
```


Если что-то пошло не так, можете обнулить регистрацию службы:
```php
if ($miniShop2 = $modx->getService('miniShop2')) {
    $miniShop2->removeService('delivery', 'msdeliverycourierhandler');
}
```


 
#### Осталось перейти в Приложения - minishop2  - Настройки 

Там создать новый тип доставки и в поле Класс-обработчик выбрать msDeliveryCourierHandler
На этом всё.
