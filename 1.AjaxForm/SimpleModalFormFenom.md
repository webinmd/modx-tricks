# Пример создания простой формы в модальном окне используя шаблонизатор Fenom
 
#### Использованы: AjaxForm, FormIt, Bootstrap (опционально, для модального окна)
 
**Задача:**<br>
Создать простую форму обратной связи в модальном окне с сохранением полей в админке

**Поля** <br>
1. Имя <br>
2. Телефон <br>
3. Email <br>
4. Сообщение  <br>
5. Файл (добавлен для примера)


**1. Вызов AjaxForm на странице**
```html
{'!AjaxForm'| snippet: [
    'snippet' => 'FormIt',
    'hooks' => 'email,FormItSaveForm',
    'form' => 'tpl.callbackForm',
    'emailTpl' => 'tpl.FormEmail',
    'emailTo' => $_modx->config.emailsender,
    'emailSubject' => 'Форма: Контакты',
    'successMessage' => 'Сообщение отправлено',
    'validate' => 'name:required,phone:required,email2:blank',
    'formName' => 'Контакты',
    'formFields' => 'name,phone,email,msg,file',
    'fieldNames' => 'name==Имя,phone==Телефон,email==Email,msg==Сообщение,file==Файл'
]}  
```

**Вызов с файловыми чанками** <br>

```html
{'!AjaxForm' | snippet: [
  'snippet' => 'FormIt',
  'hooks' => 'email,FormItSaveForm',
  'form' => '@FILE chunks/tplContactForm.tpl',
  'emailTpl' => '@FILE chunks/tplFormEmail.tpl',
  'emailTo' => $_modx->config.emailsender,
  'emailFrom' => $_modx->config.emailsender, 
  'emailSubject' => 'Форма: Контакты',
  'successMessage' => 'Сообщение отправлено',
  'validate' => '
    name:required,
    phone:required,
    email:required,
    msg:required,
    email2:blank,
  'formName' => 'Контакты',
  'formFields' => 'name,phone,email,msg,file',
  'fieldNames' => 'name==Имя,phone==Телефон,email==Email,msg==Сообщение,file==Файл'
]}
```

email2:blank - скрытое поле email используется для отсекания "глупых" ботов
Это поле добавляется в форму с классом hidden либо с input type="hidden"
Если поле будет выводится, то просто добавьте  в css 
```css
.hidden{
    display: none !important;
}
```


**2. tpl.callbackForm**
```html
<!-- обратный звонок -->
<div class="modal fade" id="callback" tabindex="-1" role="dialog" >
    <div class="modal-dialog">
        <div class="form">
            <button type="button" class="form-close" data-dismiss="modal"><span>×</span></button>
            <form action="" method="post" enctype="multipart/form-data">
                
                <div class="form-title">Заказ обратного звонка</div> 
                <div class="form-title-sub">Оставьте свои контакты и наш менеджер свяжется с вами через 15 минут</div>
                <label>Ваше имя</label>
                <input type="text" name="name"  required > 

                <label>Телефон</label>
                <input type="text" name="phone" required >

                <label>Сообщение</label>
                <textarea name="msg"></textarea>

                <label>Файл</label>
                <input type="file" name="file" >
                 
                <button type="submit" class="btn" >Заказать звонок</button>
                <input type="hidden" name="email2" value="">
    
            </form> 
        </div>
    </div>
</div>  
```


**3. tpl.FormEmail**
```html 
<p>Данные отправителя:</p> 

{if $_pls['name']?}
    Имя: <strong> {$_pls['name']}</strong> <br>
{/if}

{if $_pls['phone']?}
    Телефон: <strong> {$_pls['phone']}</strong> <br>
{/if}

{if $_pls['email']?}
    Email: <strong> {$_pls['email']}</strong> <br>
{/if}

{if $_pls['msg']?}
    Сообщение: <strong> {$_pls['msg']}</strong> <br>
{/if}

{if $_pls['file']?}
    Файл: <strong> {$_pls['file']}</strong> <br>
{/if}
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

**4.1 Пример с использованием  Fancybox**

```javascript 
$(document).on('af_complete', function(event, response) {
    if(response.success == true){
      AjaxForm.Message.success = function(message, sticky) {
        $.fancybox.open({
          src  : '#hidden-content',
          type : 'inline'
        });
      }
    } 
});
```