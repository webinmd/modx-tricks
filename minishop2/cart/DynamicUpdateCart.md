# Динамическое обновление основной корзины

**Источник:**
[modx.pro](https://modx.pro/solutions/3942-dynamic-update-of-the-basic-basket/)

### Без изменения исходников
 
**Задача:**<br>
Динамически изменять основную корзину при смене опций или добавлении/удалении товара-подарка  

**Шаги** <br>
1. Расширить класс корзины <br>
2. Изменение default.js

У MS2 есть хорошая вещь как возможность расширения класса msCartHandler. Все обновленные данные берутся $miniShop2->cart->status() и отсюда следует что нужно в этот массив данных дописать нужные нам данные, а именно список товаров которые попадут в [[+goods]]


**1.** Для начала создаем в папке core/components/minishop2/custom/cart файл msсartзroductрandler.class.php с следующим содержимым:
```php
<?php
class msCartProductHandler extends msCartHandler {
    
    public function status($data = array()) {
        $status = array(
            'total_count' => 0
            ,'total_cost' => 0
            ,'total_weight' => 0
            ,'cart' => $this->getProducts($cart)
        );
        foreach ($this->cart as $item) {
            if (empty($item['ctx']) || $item['ctx'] == $this->modx->context->key){
                $status['total_count'] += $item['count'];
                $status['total_cost'] += $item['price'] * $item['count'];
                $status['total_weight'] += $item['weight'] * $item['count'];
            }
        }
        return array_merge($data, $status);
    }

    
    public function getProducts($cart) {
        $products = '';
        $cart = $this->get();
        
        if (!$this->modx->loadClass('pdofetch', MODX_CORE_PATH . 'components/pdotools/model/pdotools/', false, true)) {return false;}
        $pdoFetch = new pdoFetch($this->modx, array());

        $tplOuter = 'msCartOuter';
        $tplRow = 'msCartRow';      
        
        $products = array('goods' => '', 'total_count' => 0, 'total_weight' => 0, 'total_cost' => 0);
        foreach ($cart as $k => $v) {           
            $product = $this->modx->getObject('msProduct', $v['id']);
            $row['id'] = $product->get(id);
            $row['key'] = $k;
            $row['count'] = $v['count'];
            $row['pagetitle'] = $product->get('pagetitle');
            $row['old_price'] = $this->ms2->formatPrice(
                $row['price'] != $v['price']
                    ? $row['price']
                    : $row['old_price']
            );
            $row['price'] = $this->ms2->formatPrice($v['price']);
            $row['weight'] = $this->ms2->formatWeight($v['weight']);
            $row['cost'] = $this->ms2->formatPrice($v['count'] * $v['price']);
            $row['thumb'] = $product->get('thumb'); 

            if (!empty($v['options']) && is_array($v['options'])) {
                foreach ($v['options'] as $key => $value) {
                    $row['option.'.$key] = $value;
                }
            }
            unset($v['options']);

            $row['idx'] = $pdoFetch->idx++; 
            $products['goods'] .= empty($tplRow)
                ? $pdoFetch->getChunk('', $row)
                : $pdoFetch->getChunk($tplRow, $row, $pdoFetch->config['fastMode']);
            $products['total_count'] += $v['count'];
            $products['total_cost'] +=  $v['count'] * $v['price'];
            $products['total_weight'] += $v['count'] * $v['weight'];
            
        }       
        
        $products['total_cost'] = $this->ms2->formatPrice($products['total_cost']);
        $products['total_weight'] = $this->ms2->formatWeight($products['total_weight']);

        return empty($tplOuter)
            ? $pdoFetch->getChunk('', $products)
            : $pdoFetch->getChunk($tplOuter, $products, $pdoFetch->config['fastMode']);
    }   
    
}
```

**Переходим в систему управления, в настройки системы/minishop2/корзина и меняем класс обработчик корзины на msCartProductHandler.**

### ВАЖНО!!! обратите внимание в строках выше есть : 
```php
$tplOuter = 'msCartOuter';
$tplRow = 'msCartRow';
```

Не забудьте создать эти чанки!
Пример msCartOuter (изменяйте под свои нужды): 
```html
<table class="table">
    [[+goods]]
</table>
<div class="cart__total">Итого: <b>  <span class="ms2_total_cost">[[+total_cost]]</span> [[%ms2_frontend_currency]]</b></div>
```

Пример msCartRow (изменяйте под свои нужды): 
```html
<tr id="[[+key]]">
    <td>
        <div class="cart-link" >
            <span>
                <img src="[[+thumb]]" alt="[[+pagetitle]]" >
            </span>
            <span><b>[[+pagetitle]]</b></span>
        </div>
    </td>
    <td><span>[[+price]]</span> [[%ms2_frontend_currency]]</td>
    <td>
        <form method="post" class="ms2_form" role="form">
            <input type="hidden" name="key" value="[[+key]]"> 
            <input type="number" name="count" value="[[+count]]" onchange="$(this).closest(miniShop2.form).submit();">  
            <button class="btn btn-default" type="submit" name="ms2_action" value="cart/change"></button>
        </form>
     </td> 
    <td>
        <form method="post" class="ms2_form">
            <input type="hidden" name="key" value="[[+key]]">
            <button class="btn-close" type="submit" name="ms2_action" value="cart/remove"></button>
        </form>
    </td>
</tr>
```

**2.** Файл assets/components/minishop2/js/web/default.js продублируйте и переименуйте (чтобы не затерлось при следующем обновлении). Новое название укажите в системных настройках минишопа  (для параметра ms2_frontend_js )

после
```javascript
miniShop2.Cart.totalCost = '.ms2_total_cost';
```
дописываем
```javascript
miniShop2.Cart.totalProducts = '.ms2_total_products';
```
и после
```javascript
$(miniShop2.Cart.totalCost).text(miniShop2.Utils.formatPrice(status['total_cost']));
```
дописываем
```javascript
$(miniShop2.Cart.totalProducts).html(status['cart']);
```

####Осталось только обернуть вызов сниппета [[!msCart]] в блок с классом «ms2_total_products».
 