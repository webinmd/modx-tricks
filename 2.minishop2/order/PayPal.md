# Настраиваем оплату PayPal в miniShop2

Перейти по ссылке и  авторизоваться
[Api access](https://www.paypal.com/businessmanage/credentials/apiAccess) 


Выбрать пункт **Manage API credentials**

![Manage API credentials](/assets/images/2020-06-24_21-14-22.png) 


Откроется страница для настройки 

Выберите пункт **Request an API signature**

![Request an API signature](/assets/images/2020-06-24_21-18-40.png) 


Далее вам выдаст 3 скрытых значения, нажмите на **SHOW**

![Request an API signature](/assets/images/2020-06-24_21-14-57.png) 
 
Затем переходим в системные настройки нашего сайта и указываем там необходимые настройки

**Для тестовых платежей (песочница)**

*ms2_payment_paypal_api_url*
https://api-3t.sandbox.paypal.com/nvp

*ms2_payment_paypal_checkout_url*
https://www.sandbox.paypal.com/webscr?cmd=_express-checkout&token= 


**Для реальных платежей**

*ms2_payment_paypal_api_url*
https://api-3t.paypal.com/nvp

*ms2_payment_paypal_checkout_url*
https://www.paypal.com/webscr?cmd=_express-checkout&token=

---------------------------

Далее данные из paypal аккаунта, которые мы получали выше
API password: *ms2_payment_paypal_pwd*
Signature: *ms2_payment_paypal_signature*
API username: *ms2_payment_paypal_user*

---------------------------

Остальные поля уже под каждый проект свои (ID ресурсов для успешной/неудачной оплаты, валюта и т.д.)