# Пример создания простой формы в модальном окне 
 
#### Использованы: AjaxForm, FormIt, If(проверка есть ли поля, чтобы не отправлять пустые), Bootstrap 
 
**Задача:**<br>
Создать простую форму обратной связи в модальном окне

**Поля** <br>
1. Имя <br>
2. Телефон
3. Email
4. Сообщение 
5. Файл (добавлен для примера)


**1. Вызов AjaxForm на странице**
```html
[[!AjaxForm?
  &snippet=`FormIt`
  &hooks=`email`
  &form=`tpl.callbackForm` 
  &emailTpl=`tpl.FormEmail`
  &emailTo=`[[++emailsender]]`
  &submitVar=`callback-form`
  &emailSubject=`Форма: Заказ обратного звонка`
  &successMessage=`Сообщение отправлено`
  &validate=`name:required,phone:required,email2:blank`
]] 
```

email2:blank - скрытое поле email используется для отсекания "глупых" ботов
Это поле добавляется в форму с классом hidden. Если поле будет выводится, то просто добавьте  в css 
```css
.hidden{
    display: none !important;
}
```


**2. tpl.callbackForm**
```html
<div class="modal fade" id="callback" tabindex="-1" role="dialog" enctype="multipart/form-data" >
    <div class="modal-dialog">
        <div class="form">
            <button type="button" class="form-close" data-dismiss="modal"><span>×</span></button>

            <form action="" method="post" class="ajax_form">                
                <div class="form-title">Заказ обратного звонка</div>  
                <label>Ваше имя</label>
                <input type="text" name="name" required > 

                <label>Телефон</label>
                <input type="text" name="phone" required >

                <label>Email</label>
                <input type="email" name="email" >

                <label>Сообщение</label>
                <textarea name="msg"></textarea>

                <label>Файл</label>
                <input type="file" name="file" >

                <input type="text" name="email2" class="hidden">
                
                <input type="submit" name="[[+submitVar]]" class="btn" value="Заказать звонок"> 
                 
                <input type="hidden" name="pagelink" value="[[~[[*id]]? &scheme=`full`]]">
                <input type="text" name="email2" value="" class="hidden">
                                
                [[+fi.success:is=`1`:then=`
                    <div class="alert alert-success">[[+fi.successMessage]]</div>
                `]]
                [[+fi.validation_error:is=`1`:then=`
                    <div class="alert alert-danger">[[+fi.validation_error_message]]</div>
                `]]
    
            </form> 

        </div>
    </div>
</div>  
 


**3. tpl.FormEmail**
```html
<p>Новое письмо на сайте  -  [[++site_name]] </p>
  
<p>Данные отправителя:</p>
[[!If? &subject=`[[+name]]` &operator=`notempty` &then=`Имя: <strong> [[+name]]</strong> <br>`]]
[[!If? &subject=`[[+phone]]` &operator=`notempty` &then=`Телефон: <strong> [[+phone]]</strong> <br>`]]
[[!If? &subject=`[[+email]]` &operator=`notempty` &then=`Email: <strong> [[+email]]</strong> <br>`]] 
[[!If? &subject=`[[+msg]]` &operator=`notempty` &then=`Сообщение: <strong> [[+msg]]</strong> <br>`]] 
[[!If? &subject=`[[+file]]` &operator=`notempty` &then=`Файл: <strong> [[+file]]</strong> <br>`]] 

<br> 
 
Страница отправки: <strong>[[+pagelink]]</strong> <br> 
```

**4. Скрыть форму после отправки**
```javascript
$(document).on('af_complete', function(event,res) {
  if(res.success) {
      $('.modal').modal('hide');
      // $('#success .form-success').html(res.message); // альтернативное окно при успешной отправке формы  
      // $('#success').modal('show');
  }
});
```

