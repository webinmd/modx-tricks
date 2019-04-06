# Минимальная сумма заказа для юзера из группы Users

#### Для юзера из группы Users минимальный заказ составляет значение в системных настройках (необходимо создать параметр в системных настройках min_order_summ)


##### Создать плагин на событие msOnSubmitOrder


```
<?php
if ($modx->event->name == 'msOnSubmitOrder'){    
    $user = $modx->getUser();
    
    if($user->isMember('Users')){ 
        
        if($cart = $order->ms2->cart->status()) {
            
            // получаем минимальную сумму заказа из системных настроек
            $min_summ = $modx->getOption('min_order_summ'); 
            
            // если необходимо получить словарь из компонента localizator
            /*
            $lang = $modx->getOption('cultureKey'); 
            $modx->lexicon->load($lang.':localizator:site');
            $error_string = $modx->lexicon('lang.order_minimal'); 
            */           
            
            if($min_summ>0){
                if($cart['total_cost'] < $min_summ) {
                    $modx->event->output("Минимальный заказ ".$min_summ." руб");
                }
            }
            
        }
    }
}

```