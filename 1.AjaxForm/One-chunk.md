
##1. Вызов
`[[FormIt?
    &hooks=\`fields,email\`
    &fieldNames=\`name==Имя,phone==Контактный телефон\`
    &emailTpl=\`email.tpl\`
]]`
ИЛИ
`{'FormIt' | snippet : [
    'hooks' => 'fields,email',
    'fieldNames' => 'name==Имя,phone==Контактный телефон',
    'emailTpl' => '@FILE chunks/email/email.tpl'
]}`

##2. Создание сниппета/хука
Создадим сниппет и укажем fields в качестве названия с таким кодом:

`<?php*$fieldNames = explode(',', $hook->formit->config['fieldNames']);foreach ($fieldNames as $key => $field) {$fIeld = explode('==', $field);$result[] = array('name' => $fIeld[1],'value' => $hook->getValue($fIeld[0]),);}$hook->setValue('fields', $modx->toJSON($result));return true;`

##3. Чанк/файл email.tpl
`{foreach $fields | fromJSON as $field}
    <li>{$field['name']}: {$field['value']}</li>
{/foreach}`