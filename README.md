Домашнее задание к занятию 6. «Troubleshooting»  
### Задача 1
Перед выполнением задания ознакомьтесь с документацией по администрированию MongoDB [ссыль](https://www.mongodb.com/docs/manual/crud/).  
Пользователь (разработчик) написал в канал поддержки, что у него уже 3 минуты происходит CRUD-операция в MongoDB и её нужно прервать.  
Вы как инженер поддержки решили произвести эту операцию:  
напишите список операций, которые вы будете производить для остановки запроса пользователя;
* Подключусь к серверу с БД для выполнения команд в MongoDB shell. 
* Далее командами найду зависшую операцию, с помощью фильтра или по имени пользователя, от которого запущена операция.  
```
use admin
db.aggregate( [
   { $currentOp : { allUsers: true, localOps: true } },
   { $match : <filter condition> } // Optional.  Specify the condition to find the op.
                                   // e.g. { "op" : "update", "ns": "mydb.someCollection" }
] )
```
В разделе lsid можно найти uuid операции  
```
"lsid" : {
      "id" : UUID("ff21e1a9-a130-4fe0-942f-9e6b6c67ea3c"),
      "uid" : BinData(0,"3pxqkATNUYKV/soT7qqKE0zC0BFb0pBz1pk4xXcSHsI=")
   },
```
* На основе полученных данных, могу завершить операцию командой
```
db.adminCommand( { killSessions: [
   { "id" : UUID("80e48c5a-f7fb-4541-8ac0-9e3a1ed224a4"), "uid" : BinData(0,"47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=") }
] } )
```
предложите вариант решения проблемы с долгими (зависающими) запросами в MongoDB.
Ответ:  
Можно использовать профилирование. Профайлер собирает информацию об операциях, которые выполняются дольше заданного значения. Т.о. сможем определить "долгие" транзакции, а  далее провеси анализ с помощью explain, но нужно учитывать, что профилирование накладывает дополнительные издержки на дисковую подсистему и ЦПУ.  
    
### Задача 2
Перед выполнением задания познакомьтесь с документацией по Redis latency troobleshooting.  [ссыль](https://redis.io/docs/management/troubleshooting/)  

Вы запустили инстанс Redis для использования совместно с сервисом, который использует механизм TTL. Причём отношение количества записанных key-value-значений к количеству истёкших значений есть величина постоянная и увеличивается пропорционально количеству реплик сервиса.  
  
При масштабировании сервиса до N реплик вы увидели, что:  
  
сначала происходит рост отношения записанных значений к истекшим,  
Redis блокирует операции записи.  
Как вы думаете, в чём может быть проблема?  
Полагаю что значение для TTL выставлено слишко большим, что приводит к росту отношения записанных значений к истекшим (новые приходят быстрее, чем уходят старые), уменьшить TTL.  
Redis блокирует записи потому что количество истекших одновременно ключей превышает 25% от общего количества.  
Из мануала:  
`if the database has many, many keys expiring in the same second, and these make up at least 25% of the current population of keys with an expire set, Redis can block in order to get the percentage of keys already expired below 25%`  
Задача 3
Вы подняли базу данных MySQL для использования в гис-системе. При росте количества записей в таблицах базы пользователи начали жаловаться на ошибки вида:

InterfaceError: (InterfaceError) 2013: Lost connection to MySQL server during query u'SELECT..... '
Как вы думаете, почему это начало происходить и как локализовать проблему?

Какие пути решения этой проблемы вы можете предложить?

Задача 4
Вы решили перевести гис-систему из задачи 3 на PostgreSQL, так как прочитали в документации, что эта СУБД работает с большим объёмом данных лучше, чем MySQL.

После запуска пользователи начали жаловаться, что СУБД время от времени становится недоступной. В dmesg вы видите, что:

postmaster invoked oom-killer

Как вы думаете, что происходит?

Как бы вы решили эту проблему?

Как cдавать задание
Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

