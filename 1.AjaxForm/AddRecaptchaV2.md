# Добавления ReCaptcha  к форме на MODX
 
#### Использованы: AjaxForm, FormIt
 
 1) Установить компонент  
ReCaptchaV2


2) Получить коды 
https://www.google.com/recaptcha/admin?#list

3) для AjaxForm вписать:

'hooks' => 'recaptchav2,email',
'validate' => 'g-recaptcha-response:required',

4) в футер добавить

<script src="https://www.google.com/recaptcha/api.js?onload=CaptchaCallback&render=explicit" async defer></script>
<script>
var CaptchaCallback = function() {
    $('.g-recaptcha').each(function(index, el) { 
        grecaptcha.render(el, { 'sitekey' : '[[++recaptchav2.site_key]]' });
     });
};
</script>

5) в шаблон формы
<div class="captcha">
  <div class="g-recaptcha" data-sitekey="[[++recaptchav2.site_key]]"></div>
</div>

6) Если стили не подходят, то размером можно управлять так: 

.captcha { 
  -webkit-transform: scale(0.9);
      -ms-transform: scale(0.9);
          transform: scale(0.9);
  margin: -10px 0 20px -15px;
}