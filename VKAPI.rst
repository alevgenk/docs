VK API
======

Общие сведения
~~~~~~~~~~~~~~

Сервис предоставляет REST API для отправки VK-сообщений через платформу 
Devino Telecom с возможностью переотправки по SMS в случае недоcтавки, а также для получения отчетов о статусах доставки сообщений.

Работа с API осуществляется с использованием протокола HTTP.

Тела запроса клиента и ответа сервера представлены в формате JSON. Данные должны быть в кодировке UTF-8.

API поддерживает базовую авторизацию через заголовок Authorization (https://en.wikipedia.org/wiki/Basic_access_authentication).


В заголовке запроса необходимо передать логин и пароль из Личного Кабинета в формате login:password в base64 кодировке, например:

Authorization: Basic dGVzdGVyOjExMTExMQ==

Подключение услуги
------------------

.. warning:: Для подключения услуги необходимо предоставить следующую информацию Вашему персональному менеджеру:

 * Название и адрес группы ВКонтакте
 * Описание Вашей компании и вида ее деятельности
 * Ссылку на Ваш сайт или мобильное приложение.
	
Используемые типы данных
------------------------

+------------+---------------------------------------------------------------------------------------------------+
| Тип данных |    Описание                									 |
+============+===================================================================================================+
|   Long     |  Положительные числа больше нуля									 |
+------------+---------------------------------------------------------------------------------------------------+
|   DateTime |  Значение, обозначающее дату и время, представляются в пакетах в формате «yyyy-MMdd hh:mm:ss»,	 |
|	     |	например, 1999-05-31 13:20:00  									 |
+------------+---------------------------------------------------------------------------------------------------+
|   Address  |  Адрес абонента. Номер мобильного телефона абонента в международном формате (в формате E.164).	 |
|	     |	Например, 79031112233, для России или 491791112233 для Германии					 |
+------------+---------------------------------------------------------------------------------------------------+
|   String   | Строка символов в формате UTF-8									 |
+------------+---------------------------------------------------------------------------------------------------+
|   Object   | Объект (включает в себя несколько полей других типов)						 |
+------------+---------------------------------------------------------------------------------------------------+

Ограничения и допустимые значения описаны далее в разделе описания пакетов протокола


Методы работы с API
~~~~~~~~~~~~~~~~~~~

Отправка сообщения
------------------
**(/send/vk)**


Платформа Devino инициирует отправку VK-сообщения абоненту в соответствии с данными, переданными в теле POST-запроса:

.. code-block:: python

  https://vk-send.devinotele.com/send/vk

В одном запросе может быть информация об отправке только одного сообщения.


**Пример тела запроса отправки сообщения**

.. code-block:: python
	
  {
  "login": "user1234",
  "message": 
      {
    "subject": "АО",
    "priority": "high",
    "routes": ["vk"],
    "validityPeriod": 30,
    "phone": "7916123456789",
    "text": "текст сообщения",
    "sms": {
        "srcAddress": "TESTSMS",
        "text": "текст сообщения",
        "validityPeriod": 60
    	   }
  	}
   }

Описание полей тела запроса отправки сообщения
----------------------------------------------

+-----------------+------------+---------------------------+--------------------------------+-----------------------+
|      Поле       | Тип данных | Допустимые занчения 	   | Описание 	    	            | Обязательное поле     |
+=================+============+===========================+================================+=======================+
|		  |	       |    			   | Нужно передавать имя           |			    |
|		  |	       |   			   | пользователя (из личного       | 		Да	    |
|	login	  |   String   |			   | кабинета от которого будет     |			    |
|		  |	       |   			   | осуществлена рассылка)         |			    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
|                                   Описание полей объекта message 						    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
|  subject     	  | String     | Строка от 1 до 11 символов| Адрес отправителя    	    |		Да	    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
|  		  | 	       |    Варианты:		   |			 	    |      	     	    |
|		  |	       |	1) "low"	   |			 	    |			    |
|  priority 	  |  String    |	2) "medium"	   | Приоритет сообщения  	    | 		Да	    |
|		  |	       |	3) "high"	   |			  	    |			    |
|		  |	       |	4) "realtime" 	   |  			  	    | 			    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
|  routes     	  | массив     |    Варианты:		   | Массив маршрутов VK порядке    |			    |
|		  | String     |    	1) "vk" 	   | использования  		    |		Да	    |	
|		  |	       | 	2) "push"	   |  				    |			    |
|		  |	       |	3) "vk", "push"	   | 		    		    |			    |
|		  |	       |	4) "push", "vk"	   |				    |			    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
|  validityPeriod | Long       | Целое число от 15 до 86400| Время жизни сообщения 	    |			    |
|		  |	       |			   | в секундах 	   	    | 		Да	    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
|  phone     	  | String     | Номер телефона в 	   | Номер телефона получателя	    |			    |			
|		  |	       | соответствии со стандартом| сообщения   		    |		Да	    |
|		  |	       | E.164, возможен + в начале| 			    	    | 			    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
| pushToken	  | String     |			   | Токен для доставки маршрутом   |	Да, если в routes   |
|		  |	       |			   | "push"			    |	есть "push"	    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
|  text     	  | String     | Строка до тысячи символов | Текст сообщения 	    	    | 		Да	    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
|  sms     	  | Object     |     			   | Информация о переотправке 	    |			    |
|		  |	       |			   |  сообщения по SMS		    |		Да	    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
|					Описание полей объекта SMS 					 	    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
| srcAddress      | String     |     			   | Имя отправителя SMS-сообщения  |		Да	    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
| text     	  | String     |    			   | Текст SMS-сообщения    	    | 		Да 	    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+
| validityPeriod  | Long       | Число от 60 до  86400	   | Время жизни SMS-сообщения	    |			    |
|	          |	       |			   |  в секундах         	    |		Да	    |
+-----------------+------------+---------------------------+--------------------------------+-----------------------+


**Пример ответа на запрос отправки сообщения**

.. code-block:: python

  {
    "code": "ok",
    "description": "",
    "result": 
		[{
        "code": "ok",
        "messageId": 3222269333010907000
   		 }],
  }

Описание полей тела ответа на запрос отправки сообщения
-------------------------------------------------------

+-----------------+------------+---------------------------+------------------------+-----------------------+
|      Поле       | Тип данных | Допустимые занчения 	   | Описание 		    | Обязательное поле     |
+=================+============+===========================+========================+=======================+
|  		  | 	       | Возможные значения	   |			    |		    	    |
|		  |	       | перечислены в таблице     | Код ответа на запрос   |			    |
|	 code	  |   String   | кодов ответа на запрос    | отправки сообщения     | 		Да	    |
|		  |	       | отправки сообщения	   |			    |			    |
+-----------------+------------+---------------------------+------------------------+-----------------------+
|  		  | 	       | Возможные значения	   | Описание ошибки	    |        	            |
|		  |	       | перечислены в таблице	   | обработки запроса 	    |			    |
|   description	  |   String   | кодов ответа на запрос    | отправки сообщения     | 		Да	    |
|		  |	       | отправки сообщения	   | (если была)	    |			    |
+-----------------+------------+---------------------------+------------------------+-----------------------+
|  result         | Object     |    			   | Информация о коде	    |  Да, если code="ok"   |		  
|	          |	       | 			   | валидации и  	    |	 	    	    |
|		  |	       |			   | ID сообщения	    |		    	    |
+-----------------+------------+---------------------------+------------------------+-----------------------+
|                                           Описание полей объекта result 				    |
+-----------------+------------+---------------------------+------------------------+-----------------------+
|  		  | 	       | Возможные значения	   |			    |      	            |
|		  |	       | перечислены в таблице     | Код валидации  	    |			    |
|   code	  | String     | кодов  валидации  	   | сообщения    	    | 		Да	    |
|		  |	       | сообщения		   |			    |			    |
+-----------------+------------+---------------------------+------------------------+-----------------------+
| messageId       | Long       |    			   | Уникальный 	    |	Да, если code="ok"  |
|		  |	       |			   | идентификатор сообщения| 		    	    |
+-----------------+------------+---------------------------+------------------------+-----------------------+

Коды ответа на запрос отправки сообщения
----------------------------------------

+-------------------+-------------------------------------+
| code		    |    description                	  |
+===================+=====================================+
|  ok               |  					  |
+-------------------+-------------------------------------+
|  validation_error |  login_not_specified		  |
+-------------------+-------------------------------------+
|  validation_error |  messages_not_specified		  |
+-------------------+-------------------------------------+
|  validation_error | invalid_json			  |
+-------------------+-------------------------------------+
|  queue_full       | login_send_queue_overflow		  |
+-------------------+-------------------------------------+
|  system_error     | Описание внутренней ошибки сервера  |
+-------------------+-------------------------------------+

Коды валидации сообщения
------------------------

+------------------------------------+---------------------------------------------+
| code			             |    Описание         		      	   |
+====================================+=============================================+
| ok                                 | Сообщение добавлено в очередь на отправку   |
+------------------------------------+---------------------------------------------+
| subject_not_specified              |  Не указан адрес отправителя		   |
+------------------------------------+---------------------------------------------+
| subject_invalid                    |  Недопустимый адрес отправителя		   |
+------------------------------------+---------------------------------------------+
| priority_not_specified             | Не указан приоритет сообщения		   |
+------------------------------------+---------------------------------------------+
| priority_invalid                   | Недопустимый приоритет сообщения		   |
+------------------------------------+---------------------------------------------+
| routes_not_specified               | 	Не указаны маршруты доставки	           |
+------------------------------------+---------------------------------------------+
|  routes_invalid                    | Недопустимый набор маршрутов доставки       |
+------------------------------------+---------------------------------------------+
|  vp_invalid                        |  Недопустимый validityPeriod		   |
+------------------------------------+---------------------------------------------+
|  phone_not_specified               |  Не указан номер телефона		   |
+------------------------------------+---------------------------------------------+
|  phone_invalid                     | Недопустимый номер телефона		   |
+------------------------------------+---------------------------------------------+
|  text_not_specified                | Не указан текст сообщения	           |
+------------------------------------+---------------------------------------------+
|  text_invalid                      | Недопустимый текст сообщения		   |
+------------------------------------+---------------------------------------------+
|  sms_text_not_specified            |  Не указан текст SMS-сообщения		   |
+------------------------------------+---------------------------------------------+
|  sms_subject_not_specified         |  Не указан номер отправителя SMS-сообщения  |
+------------------------------------+---------------------------------------------+
|  sms_validity_period_not_specified | Не указано время жизни SMS-сообщения	   |
+------------------------------------+---------------------------------------------+
|  invalid_sms_validity_period       | Недопустимое время жизни SMS-сообщения	   |
+------------------------------------+---------------------------------------------+

Получение статуса сообщения
~~~~~~~~~~~~~~~~~~~~~~~~~~~
**(/status/vk)**

Платформа Devino возвращает статус доставки ранее отправленного VK-сообщения, messageId которого был ранее передан в теле GET-запроса:

.. code-block:: python

  https://vk-send.devinotele.com/status/vk?message=<ID Вашего сообщения>
  
**Описание параметров запроса статусов**

+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|      Поле       | Тип данных | Допустимые занчения 	                   | Описание 		    | Обязательное поле     |
+=================+============+===========================================+========================+=======================+
| message	  |  Long      |  					   | Идентификатор сообщения|		Да	    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
	
**Пример ответа на запрос статусов**

.. code-block:: python

  {
    "code": "ok",
    "description": "",
    "result": 
		{
        "id": 3222269333010907000,
        "code": "ok",
        "dlvStatus": 
				{
            "status": "undelivered",
            "statusAt": "2017-07-17 08:38:49"
        			},
        "smsStates": 
				{
        "id": 3222269333010907001
        "status": "sent"
        			}
    		}
   }

Описание полей тела ответа на запрос статусов
---------------------------------------------

+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|      Поле       | Тип данных | Допустимые занчения 	                   | Описание 		    | Обязательное поле     |
+=================+============+===========================================+========================+=======================+
|  		  | 	       | Возможные значения перечислены в таблице  | Код ответа на запрос   |          Да	    |
|   code	  |  String    | кодов ответа на запрос	статусов	   | отправки сообщения     |			    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|  		  | 	       | Возможные значения перечислены в таблице  | Описание ошибки	    |          Да	    |
|		  |	       | кодов ответа на запрос	статусов	   | обработки запроса 	    |			    |
| description	  |  String    |  					   | запроса статусов 	    |			    |
|		  |	       |					   | (если была)   	    | 			    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|  result     	  | Object     |    				 	   | 			    |	  Да, если code="ok |	
|		  |	       | 			               	   | 			    |	  	            |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|                                           Описание полей объекта result 			                	    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|      id	  |  Long      |   					   | Идентификатор сообщения| 	      Да	    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
| code       	  | String     | Возможные значения перечислены в таблице  | Код валидации 	    |			    |
|		  |	       | кодов валидациисообщения идентификаторов  | идентификатора 	    |  	      Да	    |
|		  |	       | сообщений				   |			    |			    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
| dlvStatus       | Object     |    					   | Информация о статусе   |	 Да, если code="ok" |
|		  |	       |					   | сообщения		    | 		            |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
| smsStates       | Object     |    					   | Статусы доставки  	    | 	      Нет	    |
|		  |	       |					   | SMS-сообщения	    |			    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|                                           Описание полей объекта dlvStatus 				                    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|	 	  |	       | enqueued – сообщение добавлено в очередь  |			    |			    |
|		  |	       | на отправки,				   |			    |			    |
|		  |	       | sent – сообщение отправлено,		   |			    |			    |
|		  |	       | delivered – сообщение доставлено,	   |			    |			    |
|		  |	       | undelivered – сообщение отправлено, 	   | Статус доставки	    |	       Да	    |
|  status         | String     | но не доставлено,			   | сообщения VK  	    |		            |
|		  |	       | failed – сообщение не доставлено 	   |			    |			    |
|		  |	       | в результате сбоя,			   |			    |		 	    |
|		  |	       | vp_expired – сообщение не доставлено 	   |			    |			    |
|		  |	       | в течение validityPeriod  		   | 			    | 			    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
| statusAt        | DateTime   | Возможные значения перечислены в таблице  |  Время обновления      |			    |
|		  |	       |  					   |  статуса доставки 	    | 	       Да	    |
|		  |	       |					   |  сообщения VK	    |			    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
| error           | String     |  Набор всех возможных ошибок заранее      | Информация о статусе   |	       Нет	    |
|		  |	       |  не предопределен			   | сообщения		    | 			    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|                                           Описание полей объекта dlvStatus 				                    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|    id        	  | Long       |  					   | Идентификатор  	    |	       Нет	    |
|		  |	       |					   | SMS-сообщения	    | 			    |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+
|	 	  |	       | enqueued – сообщение находится в очереди  |			    |			    |
|		  |	       | на отправку,				   |			    |			    |
|		  |	       | sent – сообщение отправлено абоненту,	   | Статус SMS-сообщения   |	        Да	    |
|		  |	       | delivered – сообщение доставлено абоненту,|			    |			    |
|		  |	       | undelivered – сообщение отправлено,       |			    |  			    |
| status	  | String     | но не доставлено абоненту	           | 			    |		            |
+-----------------+------------+-------------------------------------------+------------------------+-----------------------+


Коды ответа на запрос статусов
------------------------------


+-------------------+-------------------------------------+
| code		    |    description                      |
+===================+=====================================+
|  ok               |  					  |
+-------------------+-------------------------------------+
|  validation_error |  message_not_specified		  |
+-------------------+-------------------------------------+
|  system_error     |  Описание внутренней ошибки сервера |
+-------------------+-------------------------------------+

Коды валидации идентификаторов сообщений
----------------------------------------

+-------------------+-------------------------------------+
| code		    |    description                	  |
+===================+=====================================+
|  ok               |  Известный идентификатор сообщения  |
+-------------------+-------------------------------------+
|unknown_message_id |  Неизвестный идентификатор сообщения|
+-------------------+-------------------------------------+


Получение статуса сообщения с помощью Callback-запросов
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Для получения статуса сообщения могут использоваться callback-запросы. В таком случае Платформа Devino будет отправлять POST-запрос на выбранный Вами URL каждый раз, когда у отправленного Вами сообщения будет меняться статус.
Запрос считается доставленным, если в ответ на него был получен статус HTTP(200). В противном случае будут совершаться повторные попытки доставки в течение 24 часов и по истечению этого срока статус сообщения можно будет получить только с помощью GET-запроса, описанного выше.

.. warning:: Обратите внимание, что информация о переотправке по SMS в callback-запросе не предоставляется.

.. warning:: Для получения callback-запросов от сервиса необходимо передать Вашему персональному менеджеру или в техническую поддержку (support@devinotele.com) информацию об URL, на который будут отправляться запросы.

**Пример тела callback-запроса**


.. code-block:: python

   [{
    "id":1343343,
    "status": "DELIVERED",
    "time": "2017-05-31 14:51:12"
    }]
	
  
Описание полей запроса
----------------------

+-----------------+------------+---------------------------------------------------------+-------------------+
|      Поле       | Тип данных | Описание 	                     		         | Обязательное поле |
+=================+============+=========================================================+===================+
|     id	  | Long       | Уникальный идентификатор сообщения в Платформе Devino	 |         Да  	     |
+-----------------+------------+---------------------------------------------------------+-------------------+
|   status	  | String     | Статус доставки сообщения VK	       			 |  	   Да	     |
+-----------------+------------+---------------------------------------------------------+-------------------+
|   time	  | DateTime   | Время получения статуса (по Москве, UTC+3)	         |  	   Да	     |
+-----------------+------------+---------------------------------------------------------+-------------------+
|   error	  | String     | Ошибка доставки сообщения VK (если есть)	         |  	   Да	     |
+-----------------+------------+---------------------------------------------------------+-------------------+
