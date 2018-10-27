1) tpl.msEmail
Добавить
```
<tr>
    <td></td>
    <td>{block 'address'}{/block}</td>
    <td></td>
</tr>
```

2) tpl.msEmail.new.manager

Добавить 
```
{block 'address'}
<table class="container" style="padding:0 20px 20px 20px;width: 100%;background:#fff;margin-top:10px;">
    <tr>
        <td>
          
            <h3>Контактные данные</h3>
            <p>Имя:     <b>{$address.receiver ?: $address.name}</b></p>
            <p>Телефон: <b>{$address.phone}</b></p>
            <p>e-mail:  <b>{$user.email}</b></p> 
            <p></p>
            
            <h3>Адрес доставки</h3>
            <p>Способ доставки: {$delivery.name}</p>
            <p>Индекс: <b>{$address.index}</b></p>
            <p>Город: <b>{$address.city}</b></p>
            {if $address.street?}<p>Улица: <b>{$address.street}</b></p>{/if} 
            {if $address.building?}<p>Дом: <b>{$address.building}</b></p>{/if} 
            {if $address.region?}<p>Корпус: <b>{$address.region}</b></p>{/if}  
            {if $address.room?}<p>Квартира: <b>{$address.room}</b></p>{/if}  
            <p></p> 
            
            <h3>Оплата</h3>
            <p>Способ оплаты: <b>{$payment.name}</b></p>  
            <p></p> 
            
        </td>
    </tr>
</table>
{/block}
```
