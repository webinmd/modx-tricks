# Подписывать пользователя на рассылку в модуле Sendex при оформлении заказа в minishop2 

#### Пользователь, у которого указан email (если авторизация не по телефону), добавляется в рассылку, id которой указан в плагине ниже 


##### Создать плагин на событие msOnCreateOrder, например subscribeUserOnOrder


```
<?php

switch ($modx->event->name)
{
    
    case "msOnCreateOrder":
        
        $order_data = $order->get();
          
        // проверяем наличие email
        if(empty($order_data['email'])) { 
            return; 
        }
        
        $email = $order_data['email'];
        $user = $modx->getObject("modUser", array('username' => $email) );
        $userid = $user->get('id');
 
                    
        $Sendex = $modx->getService('sendex','Sendex',$modx->getOption('sendex_core_path',null,$modx->getOption('core_path').'components/sendex/').'model/sendex/');
        if (!($Sendex instanceof Sendex)) return '';
        
        // получаем id из системных настроек, либо будет подписан на рассылку с id 1
        $idnewsletter = $modx->getOption('idnewsletter', $scriptProperties, '1');
        

        // проверка если юзер еще не подписан 
        $duble = $modx->getObject('sxSubscriber', array('email' => $email));
        if ($duble) {return;}
         
        if ($userid) {
            $obj = $modx->newObject('sxSubscriber');
            $data = array(
                    'user_id' => $userid,
                    'email' => $email,
                    'newsletter_id' => $idnewsletter,
                    );
            
            $obj->fromArray($data);
            $obj->save();
        }
            
   
    break; 
}
```