# Пример настройки smtp для gmail почты

*Перейти в системные настройки, выбрать раздел core и вписать в фильтр smtp*


Ключ |  Значение 
------------- | -------------
mail_smtp_auth | Да
mail_smtp_hosts | smtp.gmail.com
mail_smtp_pass | [пароль из приложения]
mail_smtp_port | 587
mail_smtp_prefix | tls
mail_smtp_user | [почта юзера]
mail_user_smtp | Да

В сервисе gmail.com необходимо создать приложение и пароль от него вписать в системные настройки 
Приложение создается по ссылке:
https://myaccount.google.com/apppasswords
