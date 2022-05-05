# Глава 5. Сети

Под сетями обычно подразумевается объединение множества компью
теров с целью обеспечить разного рода взаимодействия между ними.
Однако нас в первую очередь интересуют не взаимодействия между
компьютерами, а взаимодействия между процессами. При этом, с точ
ки зрения приемов, которые мы собираемся продемонстрировать, со
вершенно неважно, выполняются взаимодействующие процессы на
разных компьютерах или на одном и том же компьютере.

В этой главе рассматриваются вопросы создания программ на языке
Python, которые соединяются с другими процессами с помощью стан
дартной библиотеки socket (а также библиотек, реализованных на ос
нове библиотеки socket) и затем взаимодействуют с этими процессами.

## Сетевые клиенты.

Роль серверов – находиться в ожидании, когда клиенты соединятся
с ними, а роль клиентов – инициировать соединения. В состав стан
дартной библиотеки языка Python входят реализации множества сете
вых клиентов. В этом разделе мы обсудим наиболее типичные и часто
используемые разновидности клиентов.

**socket**

Модуль socket реализует интерфейс доступа к реализации сокетов опе
рационной системы. Это означает, что на языке Python можно реали
зовать любые действия с сокетами. На случай, если ранее вам не при
ходилось заниматься разработкой программ, работающих с сетью, эта
глава предоставляет краткий обзор средств работы в сети. Это должно
помочь вам составить представление о том, какие действия можно реа
лизовать с помощью сетевых библиотек языка Python.

Модуль socket содержит фабричную функцию socket(), которая созда
ет и возвращает объект socket. Несмотря на то, что функция socket()
может принимать большое число аргументов, определяющих тип соз
даваемого сокета, при вызове функции socket() без аргументов по
умолчанию возвращается объект сокет TCP/IP:
In [1]: import socket
```
```
In [2]: s = socket.socket()
In [3]: s.connect(('192.168.1.15', 80))
```
```
In [4]: s.send("GET / HTTP/1.0\n\n")
Out[4]: 16
```
```
In [5]: s.recv(200)
Out[5]: 'HTTP/1.1 200 OK\r\n\
Date: Mon, 03 Sep 2007 18:25:45 GMT\r\n\
Server: Apache/2.0.55 (Ubuntu) DAV/2 PHP/5.1.6\r\n\
ContentLength: 691\r\n\
Connection: close\r\n\
ContentType: text/html; charset=UTF8\r\n\
\r\n\
<!DOCTYPE HTML P'
In [6]: s.close()
```
```
В этом примере с помощью фабричной функции socket() создается объ
ект типа socket с именем s. После этого он подключается к порту 80 (но
мер порта, используемый протоколом HTTP по умолчанию) локально
го вебсервера. Затем серверу передается текстовая строка "GET / HTTP/
1.0\n\n" (которая представляет собой простейший запрос HTTP). По
сле посылки запроса сокет получает первые 200 байтов ответа сервера,
в котором содержится сообщение о состоянии "200 OK" и заголовки
HTTP. В самом конце мы закрываем соединение.
В этом примере демонстрируется использование методов объекта socket,
к которым вы, вероятно, будете обращаться наиболее часто. Метод
connect() устанавливает соединение между вашим объектом socket
и удаленным сокетом (то есть «не с этим объектом сокета»). Метод
send() выполняет передачу данных от вашего объекта socket удаленно
му хосту. Метод recv() выполняет прием любых данных, которые бы
ли переданы удаленным хостом. И метод close() закрывает соедине
ние между двумя сокетами. Этот очень простой пример демонстриру
ет, насколько легко можно создавать объекты socket и с их помощью
осуществлять передачу и прием данных.
Теперь рассмотрим немного более полезный пример. Предположим,
что у вас имеется сервер, на котором выполняется сетевое приложе
ние, такое как вебсервер. И вам необходимо следить за его работой,
чтобы гарантировать возможность соединения с ним в течение дня.
Это очень простой вид мониторинга, но он позволяет убедиться, что сервер продолжает работу и что вебсервер попрежнему ожидает со
единений с клиентами на некотором порту. Взгляните на пример 5.1.
```
```
Пример 5.1. Проверка порта TCP
#!/usr/bin/env python
```
```
import socket
import re
import sys
def check_server(address, port):
#Создать сокет TCP
s = socket.socket()
print "Attempting to connect to %s on port %s" % (address, port)
try:
s.connect((address, port))
print "Connected to %s on port %s" % (address, port)
return True
except socket.error, e:
print "Connection to %s on port %s failed: %s" % (address, port, e)
return False
```
```
if __name__ == '__main__':
from optparse import OptionParser
parser = OptionParser()
parser.add_option("a", "address", dest="address", default='localhost',
help="ADDRESS for server", metavar="ADDRESS")
parser.add_option("p", "port", dest="port", type="int", default=80,
help="PORT for server", metavar="PORT")
(options, args) = parser.parse_args()
print 'options: %s, args: %s' % (options, args)
check = check_server(options.address, options.port)
print 'check_server returned %s' % check
sys.exit(not check)
```
```
Все необходимые действия выполняются функцией check_server(). Она
создает объект socket. Затем пытается установить соединение с указан
ным номером порта по указанному адресу. Если соединение удалось
установить, функция возвращает значение True. В случае неудачи ме
тод socket.connect() возбуждает исключение, которое перехватывается
функцией, и в этом случае она возвращает значение False. В разделе
main этого сценария выполняется вызов функции check_server(). В этом
разделе выполняется разбор аргументов командной строки, получен
ных от пользователя, и переданные аргументы преобразуются в соот
ветствующий формат для последующей передачи функции check_ser
ver(). В процессе своей работы этот сценарий постоянно выводит сооб
щения о ходе выполнения. Самое последнее, что выводит сценарий, –
это возвращаемое значение функции check_server(). В качестве собст
венного кода завершения сценарий возвращает командной оболочке
значение, противоположное возвращаемому значению функции check_
server(). Сделано это для того, чтобы превратить сценарий в более или
менее полезную утилиту. Обычно утилиты, подобные этой, возвраща
ют командной оболочке значение 0 в случае успеха и некоторое другое
значение, отличное от 0 (обычно некоторое положительное число),
в случае неудачи. Ниже приводится пример вывода сценария, полу
ченного при успешном соединении с вебсервером, к которому мы уже
подключались ранее:
jmjones@dinkgutsy:code$ python port_checker_tcp.pya 192.168.1.15 p 80
options: {'port': 80, 'address': '192.168.1.15'}, args: []
Attempting to connect to 192.168.1.15 on port 80
Connected to 192.168.1.15 on port 80
check_server returned True
```
```
Последняя строка в выводе, содержащая текст check_server returned
True, означает, что соединение было благополучно установлено.
Ниже приводится пример вывода сценария, полученного в результате
неудачной попытки соединения:
```
```
jmjones@dinkgutsy:code$ python port_checker_tcp.pya 192.168.1.15 p 81
options: {'port': 81, 'address': '192.168.1.15'}, args: []
Attempting to connect to 192.168.1.15 on port 81
Connection to 192.168.1.15 on port 81 failed: (111, 'Connection refused')
check_server returned False
```
```
Последняя строка в выводе, содержащая текст check_server returned
False, означает, что попытка соединения не удалась. В предпоследней
строке вывода, содержащей текст Connection to 192.168.1.15 on port 81
failed, можно увидеть причину: 'Connection refused' (Соединение от
вергнуто). Об этом можно строить самые разнообразные предположе
ния – например, возможно, что на данном сервере нет никаких про
цессов, обрабатывающих порт с номером 81.
Мы создали три примера, чтобы продемонстрировать, как можно ис
пользовать эту утилиту в сценариях на языке командной оболочки.
Первый пример представляет собой команду, которая запускает сцена
рий и выводит слово SUCCESS (успешно) в случае успеха. Здесь был ис
пользован оператор &&, играющий роль условной инструкции ifthen:
$ python port_checker_tcp.pya 192.168.1.15 p 80 && echo "SUCCESS"
options: {'port': 80, 'address': '192.168.1.15'}, args: []
Attempting to connect to 192.168.1.15 on port 80
Connected to 192.168.1.15 on port 80
check_server returned True
SUCCESS
```
```
В этом случае сценарий благополучно установил соединение, поэтому
после того, как он выполнился и вывел результаты, командная обо
лочка напечатала слово SUCCES.
$ python port_checker_tcp.pya 192.168.1.15 p 81 && echo "FAILURE"
options: {'port': 81, 'address': '192.168.1.15'}, args: []
Attempting to connect to 192.168.1.15 on port 81
Connection to 192.168.1.15 on port 81 failed: (111, 'Connection refused')
check_server returned False
```
```
На этот раз сценарий завершился неудачей, но, несмотря на это, ко
манда не вывела слово FAILURE (неудача).
$ python port_checker_tcp.pya 192.168.1.15 p 81 || echo "FAILURE"
options: {'port': 81, 'address': '192.168.1.15'}, args: []
Attempting to connect to 192.168.1.15 on port 81
Connection to 192.168.1.15 on port 81 failed: (111, 'Connection refused')
check_server returned False
FAILURE
```
```
В этом случае сценарий тоже потерпел неудачу, но на этот раз мы за
менили оператор && оператором ||. Это просто означает, что в случае,
если сценарий возвращает признак неудачи, следует вывести слово
FAILURE, что и было сделано.
Сам факт, что сервер позволяет выполнить подключение к порту с но
мером 80, еще не означает доступность вебсервера. Более точно опреде
лить состояние вебсервера поможет тест, который определяет, спосо
бен ли вебсервер генерировать заголовки HTTP с ожидаемым кодом со
стояния для заданного URL. Сценарий в примере 5.2 реализует именно
такой тест.
```
```
Пример 5.2. Проверка веб>сервера с помощью сокетов
#!/usr/bin/env python
import socket
import re
import sys
```
```
def check_webserver(address, port, resource):
#Создать строку запроса HTTP
if not resource.startswith('/'):
resource = '/' + resource
request_string = "GET %s HTTP/1.1\r\nHost: %s\r\n\r\n" % (resource,
address)
print 'HTTP request:'
print '|||%s|||' % request_string
```
```
#Создать сокет TCP
s = socket.socket()
print "Attempting to connect to %s on port %s" % (address, port)
try:
s.connect((address, port))
print "Connected to %s on port %s" % (address, port)
s.send(request_string)
#Нам достаточно получить только первые 100 байтов
rsp = s.recv(100)
print 'Received 100 bytes of HTTP response'
print '|||%s|||' % rsp
except socket.error, e:
print "Connection to %s on port %s failed: %s" % (address, port, e)
return False
finally:
#Будучи добропорядочными программистами закроем соединение
print "Closing the connection"
s.close()
lines = rsp.splitlines()
print 'First line of HTTP response: %s' % lines[0]
try:
version, status, message = re.split(r'\s+', lines[0], 2)
print 'Version: %s, Status: %s, Message: %s' % (version,
status, message)
except ValueError:
print 'Failed to split status line'
return False
if status in ['200', '301']:
print 'Success status was %s' % status
return True
else:
print 'Status was %s' % status
return False
```
```
if __name__ == '__main__':
from optparse import OptionParser
parser = OptionParser()
parser.add_option("a", "address", dest="address", default='localhost',
help="ADDRESS for webserver", metavar="ADDRESS")
parser.add_option("p", "port", dest="port", type="int", default=80,
help="PORT for webserver", metavar="PORT")
parser.add_option("r", "resource", dest="resource",
default='index.html',
help="RESOURCE to check", metavar="RESOURCE")
```
```
(options, args) = parser.parse_args()
print 'options: %s, args: %s' % (options, args)
check = check_webserver(options.address, options.port, options.resource)
print 'check_webserver returned %s' % check
sys.exit(not check)
```
```
Как и в предыдущем примере, где основную работу выполняла функция
check_server(), в этом примере также все необходимые действия вы
полняются единственной функцией check_webserver(). Сначала функ
ция check_webserver() создает строку запроса HTTP. Протокол HTTP,
если вы не в курсе, достаточно четко регламентирует порядок взаимо
действий серверов и клиентов. Запрос HTTP, который создается в функ
ции check_webserver(), является чуть ли не самым простым запросом.
Затем функция check_webserver() создает объект socket, с его помощью устанавливает соединение с сервером и отправляет запрос HTTP. После
этого она читает ответ сервера и закрывает соединение. Когда в сокете
возникает ошибка, функция возвращает значение False, указывая тем
самым, что проверка вебсервера потерпела неудачу. Затем она берет
ответ, полученный от сервера, и извлекает из него код состояния. Если
код состояния имеет значение 200, что означает «OK» (все в порядке),
или 301, что означает «Moved Permanently» (запрошенная страница
была перемещена), функция check_webserver() возвращает значение
True, в противном случае она возвращает значение False. В разделе main
сценарий выполняет разбор аргументов командной строки, получен
ных от пользователя, и вызывает функцию check_webserver(). После
выполнения функции check_webserver() сценарий возвращает команд
ной оболочке значение, противоположное значению, полученному от
функции. Сделано это по тем же причинам, что и в предыдущем при
мере. Нам хотелось бы иметь возможность вызывать этот сценарий из
сценариев командной оболочки и определять случаи успеха или неуда
чи. Ниже приводится пример использования этого сценария:
$ python web_server_checker_tcp.pya 192.168.1.15 p 80 apache2default
options: {'resource': 'apache2default', 'port': 80, 'address':
'192.168.1.15'}, args: []
HTTP request:
|||GET /apache2default HTTP/1.1
Host: 192.168.1.15
|||
Attempting to connect to 192.168.1.15 on port 80
Connected to 192.168.1.15 on port 80
Received 100 bytes of HTTP response
|||HTTP/1.1 301 Moved Permanently
Date: Wed, 16 Apr 2008 23:31:24 GMT
Server: Apache/2.0.55 (Ubuntu) |||
Closing the connection
First line of HTTP response: HTTP/1.1 301 Moved Permanently
Version: HTTP/1.1, Status: 301, Message: Moved Permanently
Success status was 301
check_webserver returned True
```
```
Последние четыре строки вывода показывают, что код состояния HTTP
для адреса /apache2–default на этом сервере имеет значение 301, что оз
начает успех.
Ниже приводится пример повторной попытки. На этот раз мы указали
ресурс, отсутствующий на вебсервере, чтобы продемонстрировать, что
произойдет, когда функция check_webserver() вернет значение False:
$ python web_server_checker_tcp.pya 192.168.1.15 p 80 foo
options: {'resource': 'foo', 'port': 80, 'address': '192.168.1.15'}, args: []
HTTP request:
|||GET /foo HTTP/1.1
Host: 192.168.1.15
|||
Attempting to connect to 192.168.1.15 on port 80
Connected to 192.168.1.15 on port 80
Received 100 bytes of HTTP response
|||HTTP/1.1 404 Not Found
Date: Wed, 16 Apr 2008 23:58:55 GMT
Server: Apache/2.0.55 (Ubuntu) DAV/2 PH|||
Closing the connection
First line of HTTP response: HTTP/1.1 404 Not Found
Version: HTTP/1.1, Status: 404, Message: Not Found
Status was 404
check_webserver returned False
```
```
Последние четыре строки в предыдущем примере свидетельствовали
об успешной проверке, но в данном случае четыре последние строки
вывода показывают, что проверка завершилась неудачей. На этом сер
вере отсутствует ресурс с адресом /foo, поэтому функция проверки вер
нула значение False.
В этом разделе было показано, как создавать низкоуровневые утили
ты, устанавливающие соединение с серверами в сети и выполняющие
простейшие проверки. Цель этих примеров состояла в том, чтобы по
казать вам, как серверы и клиенты взаимодействуют друг с другом.
Если у вас имеется возможность написать сетевой программный ком
понент, использующий более высокоуровневую библиотеку, чем мо
дуль socket, вам следует использовать ее. Нет смысла тратить свое вре
мя на создание сетевых программных компонентов, использующих та
кую низкоуровневую библиотеку, как модуль socket.
```
**httplib**

```
Предыдущий пример продемонстрировал, как можно выполнить за
прос HTTP с помощью модуля socket. В этом примере будет показано,
как то же самое можно реализовать с помощью модуля httplib. Как
определить, когда предпочтительнее использовать модуль httplib,
а когда модуль socket? Или, в более широком смысле, когда предпоч
тительнее использовать более высокоуровневый модуль, а когда – бо
лее низкоуровневый? Одно хорошее правило, выработанное на прак
тике, гласит – всякий раз, когда имеется такая возможность, следует
использовать более высокоуровневый модуль. Возможно, вам потребу
ется нечто, что пока отсутствует в библиотеке, или вам необходимо ор
ганизовать более точное управление чемто, что уже имеется в библио
теке, или вы захотите воспользоваться преимуществами производи
тельности. Но даже в этом случае нет никаких причин полностью отка
зываться от использования такой библиотеки, как httplib, и отдавать
предпочтение такой низкоуровневой библиотеке, как socket.
Пример 5.3 реализует ту же самую функциональность, что и предыду
щий, используя для этого возможности модуля httplib.

_Пример 5.3. Проверка веб>сервера с помощью httplib_

```
#!/usr/bin/env python
import httplib
import sys

def check_webserver(address, port, resource):
#Создать соединение
if not resource.startswith('/'):
resource = '/' + resource
try:
conn = httplib.HTTPConnection(address, port)
print 'HTTP connection created successfully'
#Выполнить запрос
req = conn.request('GET', resource)
print 'request for %s successful' % resource
#Получить ответ
response = conn.getresponse()
print 'response status: %s' % response.status
except sock.error, e:
print 'HTTP connection failed: %s' % e
return False
finally:
conn.close()
print 'HTTP connection closed successfully'
if response.status in [200, 301]:
return True
else:
return False
if __name__ == '__main__':
from optparse import OptionParser
parser = OptionParser()
parser.add_option("a", "address", dest="address", default='localhost',
help="ADDRESS for webserver", metavar="ADDRESS")
```
```
parser.add_option("p", "port", dest="port", type="int", default=80,
help="PORT for webserver", metavar="PORT")
```
```
parser.add_option("r", "resource", dest="resource",
default='index.html',
help="RESOURCE to check", metavar="RESOURCE")
(options, args) = parser.parse_args()
print 'options: %s, args: %s' % (options, args)
check = check_webserver(options.address, options.port, options.resource)
print 'check_webserver returned %s' % check
sys.exit(not check)
```

Концептуально этот пример достаточно близок к предыдущему. Два
самых существенных отличия состоят в том, что здесь отсутствует не
обходимость вручную создавать строку запроса HTTP и нет никакой
необходимости вручную выполнять анализ полученного ответа. Объект соединения библиотеки httplib имеет метод request(), который конструирует и отправляет строку запроса HTTP. Кроме того, у объекта соединения имеется метод getresponse(), который создает объект
с полученным ответом. Объект ответа предоставляет возможность по
лучить код состояния ответа HTTP, обратившись к атрибуту status.
Хотя объем программного кода в этом примере уменьшился ненамно
го, тем не менее, нам удалось избавиться от необходимости вручную
создавать, отправлять и получать запрос и ответ HTTP. Да и выглядит
этот пример более аккуратным.
Ниже приводится результат запуска сценария с теми же аргументами
командной строки, которые в предыдущем примере привели к успеху.
Мы запрашиваем ресурс с адресом / на вебсервере и находим его:

```
$ python web_server_checker_httplib.pya 192.168.1.15 r /
options: {'resource': '/', 'port': 80, 'address': '192.168.1.15'}, args: []
HTTP connection created successfully
request for / successful
response status: 200
HTTP connection closed successfully
check_webserver returned True
```
```
А ниже – результат запуска сценария с аргументами командной стро
ки, которые в предыдущем примере привели к неудаче. Мы запраши
ваем ресурс с адресом /foo на вебсервере и не находим его:
```
```
$ python web_server_checker_httplib.pya 192.168.1.15 r /foo
options: {'resource': '/foo', 'port': 80, 'address': '192.168.1.15'}, args: []
HTTP connection created successfully
request for /foo successful
response status: 404
HTTP connection closed successfully
check_webserver returned False
```
```
Как уже говорилось выше, всякий раз, когда имеется возможность ис
пользовать более высокоуровневую библиотеку, следует использовать
ее. Использование httplib вместо модуля socket оказалось более про
стым и более ясным. А чем проще программный код, тем ниже вероят
ность появления ошибок в нем.
```
**ftplib**

```
Помимо модулей httplib и socket в состав стандартной библиотеки
языка Python входит также реализация клиента FTP в виде модуля
ftplib. Модуль ftplib – это полнофункциональная клиентская библио
тека для работы с протоколом FTP, которая позволяет реализовать лю
бые задачи, выполняемые приложениями FTPклиентов. Например,
с ее помощью в сценарии на языке Python можно выполнить вход на
FTPсервер, получить список файлов в определенном каталоге, загру
зить файлы, выгрузить файлы, перейти в другой каталог и выйти.
Можно даже воспользоваться одной из множества платформ, предназначенных для реализации графического интерфейса, доступных в язы
ке Python, и создать для работы с протоколом FTP свое приложение
с графическим интерфейсом.
Вместо того чтобы дать краткий обзор библиотеки, мы приведем при
мер 5.4 и затем поясним, как он работает.
```
```
Пример 5.4. Загрузка файлов с помощью ftplib
#!/usr/bin/env python
```
```
from ftplib import FTP
import ftplib
import sys
from optparse import OptionParser
```
```
parser = OptionParser()
parser.add_option("a", "remote_host_address", dest="remote_host_address",
help="REMOTE FTP HOST.",
metavar="REMOTE FTP HOST")
```
```
parser.add_option("r", "remote_file", dest="remote_file",
help="REMOTE FILE NAME to download.",
metavar="REMOTE FILE NAME")
parser.add_option("l", "local_file", dest="local_file",
help="LOCAL FILE NAME to save remote file to", metavar="LOCAL FILE NAME")
parser.add_option("u", "username", dest="username",
help="USERNAME for ftp server", metavar="USERNAME")
parser.add_option("p", "password", dest="password",
help="PASSWORD for ftp server", metavar="PASSWORD")
(options, args) = parser.parse_args()
```
```
if not (options.remote_file and
options.local_file and
options.remote_host_address):
parser.error('REMOTE HOST, LOCAL FILE NAME, ' \
'and REMOTE FILE NAME are mandatory')
if options.username and not options.password:
parser.error('PASSWORD is mandatory if USERNAME is present')
ftp = FTP(options.remote_host_address)
if options.username:
try:
ftp.login(options.username, options.password)
except ftplib.error_perm, e:
print "Login failed: %s" % e
sys.exit(1)
else:
try:
ftp.login()
except ftplib.error_perm, e:
print "Anonymous login failed: %s" % e
sys.exit(1)
try:
local_file = open(options.local_file, 'wb')
ftp.retrbinary('RETR %s' % options.remote_file, local_file.write)
finally:
local_file.close()
ftp.close()
```

В первой части сценария (сразу вслед за инструкциями, выполняющи
ми разбор аргументов командной строки) создается объект FTP вызо
вом конструктора FTP(), которому передается адрес сервера. Можно
было бы создать объект FTP, вызвав конструктор без аргументов, и за
тем вызвать метод connect(), передав ему адрес сервера FTP. Затем сце
нарий выполняет вход на сервер, используя имя пользователя и па
роль, если таковые были указаны, в противном случае выполняется
анонимный вход. Далее он создает объект типа file, куда будут сохра
няться данные, получаемые из файла на сервере FTP. После этого вы
зывается метод retrbinary() объекта FTP. Метод retrbinary(), как следу
ет из его имени, получает двоичный файл с сервера FTP. Метод прини
мает два параметра: команду, извлекающую файл, и функцию обрат
ного вызова. Обратите внимание, что в качестве функции обратного
вызова используется метод write() объекта file, который был создан на
предыдущем шаге. Важно отметить, что в этом случае мы сами не вы
зываем метод write(). Мы передаем метод write() методу retrbinary(),
чтобы он сам мог вызывать метод write(). Метод retrbinary() будет вы
зывать функцию обратно вызова при получении каждого блока дан
ных, получаемого от сервера FTP. Функция обратного вызова может
выполнять над данными любые действия. Например, эта функция
могла бы просто подсчитывать число байтов, принимаемых от сервера
FTP. Передача метода write() объекта file приводит к тому, что дан
ные, получаемые от сервера FTP, будут записаны в объект file. В за
ключение сценарий закрывает объект file и соединение с сервером
FTP. В сценарии предусматривается обработка ошибок: процедура по
лучения двоичного файла с сервера FTP заключена в инструкцию try,
а в блоке finally выполняется закрытие локального файла и соедине
ния с сервером FTP. Если случится чтото непредвиденное, файл и со
единение будут закрыты перед завершением сценария. Краткое обсу
ждение функций обратного вызова вы найдете в приложении.

**urllib**

Перемещаясь ко все более высокоуровневым модулям, входящим в со
став стандартной библиотеки, мы наконец достигли модуля urllib. Час
то, рассматривая возможность применения библиотеки urllib, предпо
лагают использовать ее для работы с протоколом HTTP, забывая, что ресурсы FTP также можно идентифицировать посредством URL. Поэтому вы, возможно, даже не предполагали, что библиотека urllib позволяет обращаться к ресурсам FTP, хотя такая возможность существует. Пример 5.5 обладает той же функциональностью, что и предыдущий пример, созданный на основе использования ftplib, но использует модуль urllib.

_Пример 5.5. Загрузка файлов с помощью urllib_

```
#!/usr/bin/env python
"""
url retriever
Порядок использования:
url_retrieve_urllib.py URL FILENAME
URL:
Если адрес URL – это адрес FTP URL, то формат адреса должен быть следующим:
ftp://[username[:password]@]hostname/filename
Если имеется необходимость использовать абсолютный путь к загружаемому файлу,
Вы должны определить URL, который выглядит примерно так:
ftp://user:password@host/%2Fpath/to/myfile.txt
Обратите внимание на последовательность '%2F' в начале пути к файлу.
FILENAME:
Абсолютный или относительный путь к локальному файлу
"""

import urllib
import sys

if 'h' in sys.argv or 'help' in sys.argv:
print __doc__
sys.exit(1)
if not len(sys.argv) == 3:
print 'URL and FILENAME are mandatory'
print __doc__
sys.exit(1)
url = sys.argv[1]
filename = sys.argv[2]
urllib.urlretrieve(url, filename)
```

Этот сценарий выглядит короче и опрятнее. Он наглядно демонстри
рует мощь библиотеки urllib. На самом деле, большую часть сценария
занимает документация, описывающая порядок его использования.
Более того, даже для анализа аргументов командной строки потребо
валось больше программного кода, чем для фактического выполнения
действий. Мы решили упростить процедуру анализа аргументов ко
мандной строки. Поскольку оба аргумента являются обязательными,
мы предпочли использовать позиционные аргументы и избавиться от
ключей. По сути, всю работу в этом примере выполняет единственная
строка:

```
urllib.urlretrieve(url, filename)
```

После получения аргументов командной строки с помощью sys.argv
эта строка обращается по указанному адресу URL и сохраняет полу
ченные данные в локальном файле с указанным именем. Этот сцена
рий будет работать как с адресами HTTP, так и с адресами FTP, и будет
работать, даже когда имя пользователя и пароль включены в URL.
Ценность этого примера заключается в следующем: если вы предполо
жили, что в языке Python некоторые действия должны выполняться
проще, чем в других языках программирования, скорее всего так оно
и окажется. Наверняка имеется какаянибудь высокоуровневая биб
лиотека, которая реализует именно то, что вам необходимо, и эта биб
лиотека входит в состав стандартной библиотеки языка Python. В дан
ном случае библиотека urllib реализует именно то, что необходимо,
и оказалось достаточно заглянуть в документацию к стандартной биб
лиотеке, чтобы узнать об этом. Впрочем, иногда вам, возможно, при
дется покидать стандартную библиотеку и искать другие ресурсы Py
thon, такие как каталог пакетов Python (Python Package Index, PyPI)
по адресу: http://pypi.python.org/pypi.

**urllib2**

Примером другой высокоуровневой библиотеки является библиотека
urllib2. Эта библиотека реализует практически те же самые функцио
нальные возможности, что и библиотека urllib. Например, urllib2 об
ладает улучшенной поддержкой аутентификации и улучшенной под
держкой cookie. Поэтому, когда вы начнете использовать библиотеку
urllib и обнаружите, что вам чегото не хватает, обратитесь к библио
теке urllib2– возможно, она лучше будет соответствовать вашим по
требностям.

### Средства вызова удаленных процедур.

Как правило, причиной создания сценариев для работы с сетью стано
вится необходимость организации взаимодействий между процессами.
Часто бывает вполне достаточно ограничиться простыми взаимодейст
виями, например, с помощью протокола HTTP или сокетов. Однако,
иногда возникает необходимость выполнять программный код в раз
ных процессах и даже на разных компьютерах, как если бы это был
один и тот же процесс. Если бы у вас имелась возможность выполнять
программный код удаленно, в некотором другом процессе, запущен
ном из программы на языке Python, то вы, скорее всего, хотели бы,
чтобы возвращаемые значения таких удаленных вызовов были объек
тами языка Python, работать с которыми намного проще, чем с фраг
ментами текста, которые необходимо анализировать вручную. Так
вот, существует несколько инструментов, позволяющих организовать
вызов удаленных процедур (Remote Procedure Call, RPC).

**XML-RPC**

Технология XMLRPC, позволяющая организовать вызов удаленных
процедур, основана на обмене специально сформированными докумен
тами XML между двумя процессами. Однако пусть вас не беспокоит
часть XML в названии – вам, скорее всего, даже не придется вникать
в формат документов, которыми будут обмениваться процессы. Един
ственное, что вам действительно необходимо знать, чтобы использо
вать технологию XMLRPC – это то, что в стандартной библиотеке
языка Python имеются реализации как клиентской, так и серверной
частей этой технологии. К тому же, вам полезно будет узнать, что реа
лизации XMLRPC имеются в большинстве языков программирова
ния и что эта технология очень проста в использовании.
В примере 5.6 приводится реализация простого сервера XMLRPC.

```
Пример 5.6. Простой сервер XML>RPC
#!/usr/bin/env python
import SimpleXMLRPCServer
import os
def ls(directory):
try:
return os.listdir(directory)
except OSError:
return []

def ls_boom(directory):
return os.listdir(directory)

def cb(obj):
print "OBJECT::", obj
print "OBJECT.__class__::", obj.__class__
return obj.cb()

if __name__ == '__main__':
s = SimpleXMLRPCServer.SimpleXMLRPCServer(('127.0.0.1', 8765))
s.register_function(ls)
s.register_function(ls_boom)
s.register_function(cb)
s.serve_forever()
```

Этот сценарий создает новый объект SimpleXMLRPCServer и связывает его с портом 8765 и с петлевым интерфейсом, имеющим IPадрес 127.0.0.1,
что делает его доступным только для процессов, выполняющихся на данном компьютере. Затем сценарий регистрирует функции ls() и ls_
boom(), которые определены тут же, в сценарии. Назначение функции cb() мы объясним чуть погодя. Функция ls() с помощью os.listdir()
получает содержимое указанного каталога и возвращает его в виде списка. Функция ls() маскирует любые исключения OSError, которые только могут возникнуть. Функция ls_boom() не выполняет обработку исключений и возвращает их клиенту XMLRPC. После этого сценарий входит в бесконечный цикл serve_forever(), в котором он ожидает поступления запросов от клиентов и обрабатывает их. Ниже приводится пример взаимодействия с этим сервером в оболочке IPython:

```
In [1]: import xmlrpclib
In [2]: x = xmlrpclib.ServerProxy('http://localhost:8765')
```
```
In [3]: x.ls('.')
Out[3]:
['.svn',
'web_server_checker_httplib.py',
....
'subprocess_arp.py',
'web_server_checker_tcp.py']
In [4]: x.ls_boom('.')
Out[4]:
['.svn',
'web_server_checker_httplib.py',
....
'subprocess_arp.py',
'web_server_checker_tcp.py']
```
```
In [5]: x.ls('/foo')
Out[5]: []
```
```
In [6]: x.ls_boom('/foo')
```
```
<class 'xmlrpclib.Fault'> Traceback (most recent call last)
...
.
.
<<большой блок диагностической информации>>
.
.
...
786 if self._type == "fault":
> 787 raise Fault(**self._stack[0])
788 return tuple(self._stack)
789
```
```
<class 'xmlrpclib.Fault'>: <Fault 1: "<type 'exceptions.OSError'>
:[Errno 2] No such file or directory: '/foo'">
(:[Errno 2] Нет такого файла или каталога: '/foo')
```
```
Прежде всего мы создали объект ServerProxy, указав ему адрес сервера
XMLRPC. Затем мы вызвали функцию x.ls('.'), чтобы получить со
держимое текущего рабочего каталога. Сервер был запущен из катало
га, содержащего программный код примеров к этой книге, поэтому спи
сок включает файлы примеров. Самое интересное, что на стороне кли
ента функция x.ls('.') возвращает список языка Python. Независимо
от языка реализации сервера – Java, Perl, Ruby или C# – можно рас
считывать на получение подобного результата. На языке реализации
сервера может быть получен перечень файлов в каталоге, создан спи
сок, массив или коллекция имен файлов; после этого программный
код сервера XMLRPC может преобразовать этот список или массив
в формат XML и передать его обратно клиенту. Мы также попробовали
вызвать функцию ls_boom(). Благодаря тому, что в функции ls_boom()
не предусматривается обработка исключений, в отличие от ls(), мы
смогли увидеть, как исключение передается от сервера клиенту. Более
того, на стороне клиента мы увидели даже диагностическую инфор
мацию.
Возможности функциональной совместимости, которыми обладает
технология XMLRPC, безусловно интересны. Но гораздо более инте
ресен тот факт, что существует возможность написать некоторый про
граммный код, запустить его на произвольном числе машин и затем
вызывать этот код удаленно в случае необходимости.
Однако в технологии XMLRPC имеются свои ограничения. Эти огра
ничения могут представлять определенную проблему, или сама техно
логия может не соответствовать нуждам и чаяниям разработчика. На
пример, когда удаленному программному коду передается нестандарт
ный объект на языке Python, библиотека XMLRPC преобразует этот
объект в словарь, переводит его в формат XML и отправляет удаленной
стороне. Безусловно, вы сможете обработать эту ситуацию, но для это
го потребуется написать программный код, который будет извлекать
данные из XMLверсии словаря, чтобы превратить его обратно в ори
гинальный объект. Так почему бы не использовать объекты непосред
ственно на сервере RPC, чтобы избежать таких сложностей с преобра
зованиями? Это нельзя сделать с помощью XMLRPC, но существуют
другие возможности.
```
**Pyro**

```
Pyro – это платформа, которая лишена некоторых недостатков, свой
ственных XMLRPC. Название Pyro происходит от Python Remote Ob
jects (удаленные объекты Python). Она позволяет реализовать те же са
мые действия, которые позволяет XMLRPC, но вместо того, чтобы
преобразовывать объекты в форму словаря, она обеспечивает возмож
ность передачи информации о типе вместе с самим объектом. Если вы
действительно захотите воспользоваться платформой Pyro, вам при
дется установить ее отдельно. Она не поставляется вместе с Python.
Кроме того, вы должны понимать, что Pyro работает только со сцена
риями на языке Python, тогда как технология XMLRPC в состоянии
обеспечить взаимодействие между сценариями на языке Python и про
граммами, написанными на других языках. В примере 5.7 приводится
реализация той же самой функции ls(), что и в примере, использую
щем технологию XMLRPC.
```

Средства вызова удаленных процедур **203**

```
Пример 5.7. Простой сервер Pyro
#!/usr/bin/env python
```
```
import Pyro.core
import os
from xmlrpc_pyro_diff import PSACB
class PSAExample(Pyro.core.ObjBase):
```
```
def ls(self, directory):
try:
return os.listdir(directory)
except OSError:
return []
def ls_boom(self, directory):
return os.listdir(directory)
def cb(self, obj):
print "OBJECT:", obj
print "OBJECT.__class__:", obj.__class__
return obj.cb()
if __name__ == '__main__':
Pyro.core.initServer()
daemon=Pyro.core.Daemon()
uri=daemon.connect(PSAExample(),"psaexample")
print "The daemon runs on port:",daemon.port
print "The object's uri is:",uri
daemon.requestLoop()
```
```
Пример на базе Pyro похож на пример XMLRPC. Сначала мы создали
класс PSAExample с методами ls(), ls_boom() и cb(). Затем из глубин Pyro
был извлечен демон. После этого мы связали объект PSAExample с демо
ном. Наконец, мы запустили демон для обслуживания запросов.
Ниже приводится сеанс взаимодействия с сервером Pyro в оболочке
IPython:
```
```
In [1]: import Pyro.core
/usr/lib/python2.5/sitepackages/Pyro/core.py:11: DeprecationWarning:
The sre module is deprecated, please import re.
import sys, time, sre, os, weakref
```
```
In [2]: psa = Pyro.core.getProxyForURI("PYROLOC://localhost:7766/psaexample")
Pyro Client Initialized. Using Pyro V3.5
```
```
In [3]: psa.ls(".")
Out[3]:
['pyro_server.py',
....
'subprocess_arp.py',
'web_server_checker_tcp.py']
```
```
In [4]: psa.ls_boom('.')
```

**204** Глава 5. Сети

```
Out[4]:
['pyro_server.py',
....
'subprocess_arp.py',
'web_server_checker_tcp.py']
In [5]: psa.ls("/foo")
Out[5]: []
In [6]: psa.ls_boom("/foo")
```
```
<type 'exceptions.OSError'> Traceback (most recent call last)
```
```
/home/jmjones/local/Projects/psabook/oreilly/<ipython console> in <module>()
.
.
...
<<большой блок диагностической информации>>
...
.
.
> 115 raise self.excObj
116 def __str__(self):
117 s=self.excObj.__class__.__name__
<type 'exceptions.OSError'>: [Errno 2] No such file or directory: '/foo'
(type 'exceptions.OSError'>: [Errno 2] Нет такого файла или каталога: '/foo')
```
```
Отлично. Мы получили те же результаты, что и в примере XMLRPC.
Именно этого мы и ожидали. Но что произойдет, если попробовать пе
редать нестандартный объект? Попробуем определить новый класс,
создать из него объект и передать его функции cb() в реализации на
основе XMLRPC и методу cb() в реализации на основе Pyro. В приме
ре 5.8 приводится фрагмент программного кода, который будет вы
полняться.
```
```
Пример 5.8. Различия между XML>RPC и Pyro
import Pyro.core
import xmlrpclib
class PSACB:
def __init__(self):
self.some_attribute = 1
```
```
def cb(self):
return "PSA callback"
```
```
if __name__ == '__main__':
cb = PSACB()
```
```
print "PYRO SECTION"
print "*" * 20
psapyro = Pyro.core.getProxyForURI("PYROLOC://localhost:7766/psaexample")
print ">>", psapyro.cb(cb)
print "*" * 20
print "XMLRPC SECTION"
print "*" * 20
psaxmlrpc = xmlrpclib.ServerProxy('http://localhost:8765')
print ">>", psaxmlrpc.cb(cb)
print "*" * 20
```
```
Обращение к функции cb() в обеих реализациях, XMLRPC и Pyro,
должно привести к вызову метода cb() переданного объекта. И в обоих
случаях этот метод должен вернуть строку PSA callback. Ниже показа
но, что произошло, когда мы запустили этот сценарий:
```
```
jmjones@dinkgutsy:code$ python xmlrpc_pyro_diff.py
/usr/lib/python2.5/sitepackages/Pyro/core.py:11: DeprecationWarning:
The sre module is deprecated, please import re.
import sys, time, sre, os, weakref
PYRO SECTION
********************
Pyro Client Initialized. Using Pyro V3.5
>> PSA callback
********************
XMLRPC SECTION
********************
>>
Traceback (most recent call last):
File "xmlrpc_pyro_diff.py", line 23, in <module>
print ">>", psaxmlrpc.cb(cb)
File "/usr/lib/python2.5/xmlrpclib.py", line 1147, in __call__
return self.__send(self.__name, args)
File "/usr/lib/python2.5/xmlrpclib.py", line 1437, in __request
verbose=self.__verbose
File "/usr/lib/python2.5/xmlrpclib.py", line 1201, in request
return self._parse_response(h.getfile(), sock)
File "/usr/lib/python2.5/xmlrpclib.py", line 1340, in _parse_response
return u.close()
File "/usr/lib/python2.5/xmlrpclib.py", line 787, in close
raise Fault(**self._stack[0])
xmlrpclib.Fault: <Fault 1: "<type 'exceptions.AttributeError'>:'dict' object
has no attribute 'cb'">
(xmlrpclib.Fault: <Fault 1: "<type 'exceptions.AttributeError'>:'dict' объект
не имеет атрибут 'cb'">)
```
Реализация на основе Pyro работает, но реализация на основе XML
RPC потерпела неудачу и оставила нас наедине с кучей диагностиче
ской информации. Последняя строка в этом блоке информации сооб
щает, что в объекте dict отсутствует атрибут cb. Эта строка обретет
смысл, если взглянуть на вывод, полученный от сервера XMLRPC.
Вспомните, что в функции cb() имеется пара инструкций print, кото
рые выводят дополнительную информацию о том, что происходит. Ни
же приводится вывод сервера XMLRPC:

```
OBJECT:: {'some_attribute': 1}
OBJECT.__class__:: <type 'dict'>
localhost [17/Apr/2008 16:39:02] "POST /RPC2 HTTP/1.0" 200 –
```

После преобразования в словарь объекта, который был создан в клиен
те, реализованном на базе XMLRPC, атрибут some_attribute превра
тился в ключ словаря. Атрибут сохранился при передаче объекта на
сервер, а метод cb() был утрачен.
Ниже приводится вывод сервера Pyro:

```
OBJECT: <xmlrpc_pyro_diff.PSACB instance at 0x9595a8>
OBJECT.__class__: xmlrpc_pyro_diff.PSACB
```

Обратите внимание, что класс объекта – PSACB, т.е. тот, который и был
создан. На стороне сервера на основе Pyro мы должны включить про
граммный код, импортирующий тот же программный код, который
используется клиентом. Это означает, что сервер Pyro вынужден им
портировать программный код клиента. Для сериализации объектов
платформа Pyro использует стандартный модуль pickle, что объясня
ет, почему Pyro обладает схожим поведением.
Подводя итоги, можно сказать: если вам необходимо простое решение
RPC, не имеющее внешних зависимостей, если вам не мешают имею
щиеся ограничения XMLRPC, и вам важна поддержка функциональ
ной совместимости с другими языками программирования, то, скорее
всего, хорошим выбором будет XMLRPC. С другой стороны, если огра
ничения XMLRPC слишком тесны для вас, вы не возражаете против
установки дополнительных библиотек и предполагаете ограничиться
только языком Python, то наилучшим вариантом для вас будет Pyro.

### SSH

SSH – это невероятно мощный и широко используемый протокол. Его
можно также воспринимать и как инструмент, потому что наиболее
распространенная его реализация носит то же самое имя. SSH обеспе
чивает безопасное соединение с удаленным сервером, выполнение ко
манд оболочки, передачу файлов и переназначение портов в обоих на
правлениях через соединение.
Если у вас имеется утилита командной строки ssh, почему бы тогда не
воспользоваться протоколом SSH в сценарии? Вся прелесть здесь со
стоит в том, что вы получаете всю мощь протокола SSH в комбинации
с широкими возможностями языка Python.
Протокол SSH2 реализован на языке Python в виде библиотеки с име
нем paramiko. Из сценариев, которые содержат только программный
код на языке Python, вы можете организовать подключение к серверу
SSH и реализовать выполнение задач SSH. В примере 5.9 демонстри
руется, как можно выполнить соединение с сервером SSH и выполнить
простую команду.

_Пример 5.9. Соединение с сервером SSH и выполнение команды в удаленной системе_

```
#!/usr/bin/env python
import paramiko

hostname = '192.168.1.15'
port = 22
username = 'jmjones'
password = 'xxxYYYxxx'
if __name__ == "__main__":
paramiko.util.log_to_file('paramiko.log')
s = paramiko.SSHClient()
s.load_system_host_keys()
s.connect(hostname, port, username, password)
stdin, stdout, stderr = s.exec_command('ifconfig')
print stdout.read()
s.close()
```

Как видно из листинга, мы импортируем модуль paramiko и определяем
три переменные. Затем создаем объект SSHClient. После этого произво
дится загрузка ключей хоста, которые в операционной системе Linux
извлекаются из файла «known_hosts». Затем выполняется соединение
с сервером SSH. Ни в одном из этих действий нет ничего сложного,
особенно, если вы уже знакомы с SSH.
Теперь мы готовы выполнить команду в удаленной системе. Метод
exec_command() выполняет указанную команду и возвращает три файло
вых дескриптора, ассоциированных с выполняемой командой: стан
дартный ввод, стандартный вывод и стандартный вывод сообщений об
ошибках. Чтобы показать, что команда выполняется на машине с IPад
ресом, который был использован для создания соединения SSH, мы вы
вели результаты выполнения команды ifconfig на удаленном сервере:

```
jmjones@dinkbuntu:~/code$ python paramiko_exec.py
eth0 Link encap:Ethernet HWaddr XX:XX:XX:XX:XX:XX
inet addr:192.168.1.15 Bcast:192.168.1.255 Mask:255.255.255.0
inet6 addr: xx00::000:x0xx:xx0x:0x00/64 Scope:Link
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
RX packets:9667336 errors:0 dropped:0 overruns:0 frame:0
TX packets:11643909 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:1000
RX bytes:1427939179 (1.3 GiB) TX bytes:2940899219 (2.7 GiB)
lo Link encap:Local Loopback
inet addr:127.0.0.1 Mask:255.0.0.0
inet6 addr: ::1/128 Scope:Host
UP LOOPBACK RUNNING MTU:16436 Metric:1
RX packets:123571 errors:0 dropped:0 overruns:0 frame:0
TX packets:123571 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:0
RX bytes:94585734 (90.2 MiB) TX bytes:94585734 (90.2 MiB)
```

Выглядит так, как если бы мы выполнили команду ifconfig на локаль
ной машине, только IPадрес отличается.
В примере 5.10 показано, как можно с помощью модуля paramiko вы
полнять передачу файлов по протоколу SFTP между удаленной и ло
кальной машинами. В данном случае пример только получает файлы
с удаленной машины, используя для этого метод get(). Если у вас воз
никнет потребность передать файл на удаленную машину, вы можете
воспользоваться методом put().
```
```
Пример 5.10. Получение файлов с сервера SSH
#!/usr/bin/env python
```
```
import paramiko
import os
```
```
hostname = '192.168.1.15'
port = 22
username = 'jmjones'
password = 'xxxYYYxxx'
dir_path = '/home/jmjones/logs'
if __name__ == "__main__":
t = paramiko.Transport((hostname, port))
t.connect(username=username, password=password)
sftp = paramiko.SFTPClient.from_transport(t)
files = sftp.listdir(dir_path)
for f in files:
print 'Retrieving', f
sftp.get(os.path.join(dir_path, f), f)
t.close()
```
```
В случае, если вы захотите использовать открытые/закрытые ключи
вместо паролей, в примере 5.11 приводится модифицированная вер
сия сценария, выполняющего команду в удаленной системе, с исполь
зованием ключа RSA.
```
```
Пример 5.11. Соединение с сервером SSH и удаленное выполнение команды –
с использованием закрытого ключа
#!/usr/bin/env python
import paramiko
```
```
hostname = '192.168.1.15'
port = 22
username = 'jmjones'
pkey_file = '/home/jmjones/.ssh/id_rsa'
```
```
if __name__ == "__main__":
key = paramiko.RSAKey.from_private_key_file(pkey_file)
s = paramiko.SSHClient()
s.load_system_host_keys()
s.connect(hostname, port, pkey=key)
stdin, stdout, stderr = s.exec_command('ifconfig')
print stdout.read()
s.close()
```

И в примере 5.12 приводится модифицированная версия сценария,
выполняющего передачу файлов и использующего ключ RSA.

```
Пример 5.12. Получение файлов с сервера SSH
#!/usr/bin/env python
import paramiko
import os
hostname = '192.168.1.15'
port = 22
username = 'jmjones'
dir_path = '/home/jmjones/logs'
pkey_file = '/home/jmjones/.ssh/id_rsa'
```
```
if __name__ == "__main__":
key = paramiko.RSAKey.from_private_key_file(pkey_file)
t = paramiko.Transport((hostname, port))
t.connect(username=username, pkey=key)
sftp = paramiko.SFTPClient.from_transport(t)
files = sftp.listdir(dir_path)
for f in files:
print 'Retrieving', f
sftp.get(os.path.join(dir_path, f), f)
t.close()
```
**Twisted**

```
Twisted – это платформа разработки сетевых приложений на языке
Python, управляемых событиями. Эта платформа позволяет решать
практически любые задачи, имеющие отношение к работе в сети. Как
и любое единое комплексное решение, эта платформа отличается вы
сокой сложностью. Twisted начнет становиться понятной только после
неоднократной работы с ней, а в самом начале работа с платформой мо
жет оказаться непростым делом. Кроме того, изучение Twisted пред
ставляет собой настолько большой труд, что поиск отправного пункта
в решении конкретной проблемы часто может оказаться устрашаю
щим.
Тем не менее, мы настоятельно рекомендуем познакомиться с этой
платформой и оценить, насколько она соответствует вашему образу
мыслей. Если вы легко сможете настроиться на «твистовое» мышле
ние, то изучение платформы Twisted скорее всего будет ценным вло
жением усилий. Отличной отправной точкой в изучении может слу
жить книга «Twisted Network Programming Essentials» Эйба Феттига
(Abe Fettig) (O’Reilly). Эта книга поможет уменьшить влияние отрица
тельных факторов, о которых упоминалось выше.
Twisted – это сетевая платформа, управляемая событиями, то есть вам
придется сконцентрироваться не на написании программного кода,
который инициализирует созданные соединения и закрывает их, и на
низкоуровневых деталях приема данных, а на программном коде, об
рабатывающем происходящие события.
Какие преимущества вы получите при использовании платформы
Twisted? Эта платформа стимулирует, а иногда даже вынуждает вас
разбивать свои задачи на маленькие части. Организация сетевого со
единения отделена от логики обработки событий, происходящих по
сле установления соединения. Эти два фактора до некоторой степени
обеспечивают автоматическую возможность повторного использова
ния вашего программного кода. Еще одно преимущество, которое не
сет в себе применение платформы Twisted, заключается в том, что вам
не придется беспокоиться о низкоуровневых подключениях и зани
маться обработкой ошибок, возникающих в них. Ваша основная зада
ча заключается в том, чтобы решить, что необходимо предпринять при
появлении определенных событий.
В примере 5.13 приводится сценарий, проверяющий сетевой порт и реа
лизованный на платформе Twisted. Это очень простой сценарий, но он
наглядно демонстрирует управляемую событиями природу Twisted,
в чем вы убедитесь при изучении пояснений к программному коду. Но
перед этим мы коснемся нескольких основных концепций, которые
вам необходимо знать. В число этих концепций входят реакторы, фаб
рики, протоколы и отложенное выполнение. Реакторы – это основа
главного цикла обработки событий любого приложения, основанного
на платформе Twisted. Реакторы занимаются доставкой событий, сете
выми взаимодействиями и многопоточным выполнением. Фабрики от
вечают за создание новых экземпляров протоколов. Каждый экземп
```
### Twisted

```
У большинства из тех, кто пишет программный код, складыва
ется отчетливая аналогия логике выполнения программы или
сценария: это похоже на ручей, текущий по склону холма, ны
ряющий в провалы, ветвящийся и т. п. Такой программный код
легко писать и отлаживать. Программный код, использующий
особенности платформы Twisted, совершенно иной. Вследствие
асинхронной природы его скорее можно сравнить с падающими
каплями дождя, чем с ручьем, текущим по склону, но на этом
аналогии и заканчиваются. Платформа вводит новый компо
нент: перехватчик событий (реактор) сотоварищи. Чтобы напи
сать и отладить программный код, использующий Twisted, при
дется отказаться от привычных убеждений и начать изобретать
аналогии под другую логику выполнения.


ляр фабрики может порождать экземпляры только одного типа прото
кола. Протоколы определяют принцип действия определенного соеди
нения. Во время выполнения приложения для каждого соединения
создается свой экземпляр протокола. А механизм отложенного выпол
нения обеспечивает возможность объединения действий в цепочки.
```
```
Пример 5.13. Проверка порта, реализованная на платформе Twisted
#!/usr/bin/env python
from twisted.internet import reactor, protocol
import sys
class PortCheckerProtocol(protocol.Protocol):
def __init__(self):
print "Created a new protocol"
def connectionMade(self):
print "Connection made"
reactor.stop()
class PortCheckerClientFactory(protocol.ClientFactory):
protocol = PortCheckerProtocol
def clientConnectionFailed(self, connector, reason):
print "Connection failed because", reason
reactor.stop()
```
```
if __name__ == '__main__':
host, port = sys.argv[1].split(':')
factory = PortCheckerClientFactory()
print "Testing %s" % sys.argv[1]
reactor.connectTCP(host, int(port), factory)
reactor.run()
```
```
Обратите внимание, что здесь мы определили два класса (PortChecker
Protocol и PortCheckerClientFactory), каждый из которых наследует клас
сы платформы Twisted. Мы связали свою фабрику PortCheckerClient
Factory с PortCheckerProtocol, присвоив класс PortCheckerProtocol атри
буту protocol класса PortCheckerClientFactory. Если попытка установить
соединение окончится неудачей, будет вызван метод фабрики client
ConnectionFailed(). Метод clientConnectionFailed() является общим для
всех фабрик платформы Twisted, и это единственный метод, который
мы определили для нашей фабрики. Определяя метод, «поставляе
мый» вместе с фабричным классом, мы переопределили поведение это
го класса, заданное по умолчанию. Когда на стороне клиента попытка
установить соединение терпит неудачу, мы выводим соответствующее
сообщение и останавливаем работу реактора.
PortCheckerProtocol – это представитель протоколов, о которых говори
лось выше. Экземпляр этого класса будет создан сразу же после того,
как будет установлено соединение с сервером, порт которого проверяет
сценарий. В классе PortCheckerProtocol мы определили единственный
метод: connectionMade(). Этот метод является общим для всех классов
протоколов платформы Twisted. Определяя этот метод, мы тем самым
переопределяем поведение по умолчанию. Когда соединение будет
благополучно установлено, платформа Twisted вызовет метод connec
tionMade() этого протокола. Как видно из сценария, этот метод просто
выводит сообщение и останавливает реактор. (К реакторам мы подой
дем очень скоро.)
Здесь оба метода – connectionMade() и clientConnectionFailed() – демон
стрируют «управляемую событиями» природу платформы Twisted. Ус
тановленное соединение – это событие. Точно так же событием являет
ся и неудача при попытке установить соединение. Когда возникают та
кие события, платформа Twisted вызывает соответствующие методы,
выполняющие их обработку, которые так и называются – обработчики
событий.
В основном разделе этого сценария мы создаем экземпляр класса
PortCheckerClientFactory. Затем предписываем реактору платформы
Twisted с помощью заданной фабрики выполнить подключение к за
данному порту указанного сервера (эти значения передаются сцена
рию как аргументы командной строки). После того как реактору будет
дано указание подключиться к заданному порту указанного сервера,
мы запускаем его в работу. Если этого не сделать, тогда вообще ничего
не произойдет.
С точки зрения хронологического порядка выполнения можно сказать,
что мы запускаем реактор после того, как дадим ему указание. В дан
ном случае было указано установить соединение с портом сервера и ис
пользовать класс PortCheckerClientFactory для доставки событий. Если
попытка соединения с указанным портом заданного хоста потерпит
неудачу, цикл обработки событий вызовет метод clientConnectionFai
led() класса PortCheckerClientFactory. Если соединение будет успешно
установлено, фабрика создаст экземпляр протокола PortCheckerProto
col и вызовет метод connectionMade() этого экземпляра. Завершится ли
попытка подключения успехом или неудачей, соответствующий обра
ботчик события остановит реактор и программа завершит свою работу.
Это был очень простой пример, но он демонстрирует суть управляемой
событиями природы платформы Twisted. Ключевыми концепциями
программирования Twisted, которые не были продемонстрированы
в этом примере, являются идея отложенного выполнения и функции
обратного вызова. Механизм отложенного выполнения берет на себя
обязательство выполнить запрошенное действие. А функции обратно
го вызова обеспечивают способ, дающий возможность определить тре
буемое действие. Функции, выполняющие отложенные действия, мо
гут объединяться в цепочки и передавать результаты друг другу. Эта
особенность платформы Twisted действительно очень сложна для по
нимания. (Механизм отложенных действий демонстрируется в приме
ре 5.14.)
В примере 5.14 представлен сценарий, использующий брокер перспек
тивы (Perspective Broker) – уникальный механизм вызова удаленных
процедур (RPC) в Twisted. Этот пример представляет собой еще одну
реализацию сервера «ls», который ранее в этой же главе был реализо
ван с использованием XMLRPC и Pyro. В первую очередь рассмотрим
реализацию сервера.

```
Пример 5.14. Сервер брокера перспективы на платформе Twisted
import os
from twisted.spread import pb
from twisted.internet import reactor
```
```
class PBDirLister(pb.Root):
def remote_ls(self, directory):
try:
return os.listdir(directory)
except OSError:
return []
```
```
def remote_ls_boom(self, directory):
return os.listdir(directory)
```
```
if __name__ == '__main__':
reactor.listenTCP(9876, pb.PBServerFactory(PBDirLister()))
reactor.run()
```

В этом примере определяется единственный класс, PBDirLister. Это
класс брокера перспективы (PB), который действует как удаленный
объект, когда клиент соединяется с ним. В этом примере данный класс
определяет всего два метода: remote_ls() и remote_ls_boom(). Метод remo
te_ls() – это один из удаленных методов, которые будут вызываться
клиентом. Этот метод remote_ls() просто возвращает содержимое ука
занного каталога. Метод remote_ls_boom() выполняет те же действия,
что и метод remote_ls(), за исключением того, что он не предусматри
вает обработку исключений. В главном разделе примера мы предписы
ваем брокеру перспективы присоединиться к порту с номером 9876
и запустить реактор.
```
```
Пример 5.15. Клиент брокера перспективы платформы Twisted
#!/usr/bin/python
```
```
from twisted.spread import pb
from twisted.internet import reactor
```
```
def handle_err(reason):
print "an error occurred", reason
reactor.stop()
def call_ls(def_call_obj):
return def_call_obj.callRemote('ls', '/home/jmjones/logs')
def print_ls(print_result):
print print_result
reactor.stop()
```
```
if __name__ == '__main__':
factory = pb.PBClientFactory()
reactor.connectTCP("localhost", 9876, factory)
d = factory.getRootObject()
d.addCallback(call_ls)
d.addCallback(print_ls)
d.addErrback(handle_err)
reactor.run()
```
```
В этом сценарии клиента определяются три функции: handle_err(),
call_ls() и print_ls(). Функция handle_err() обрабатывает все возни
кающие ошибки. Функция call_ls() инициирует вызов удаленного ме
тода «ls». Функция print_ls() выводит результаты вызова удаленного
метода «ls». Может показаться немного странным, что одна функция
инициирует вызов удаленного метода, а другая выводит результаты
этого вызова. Но, так как Twisted является асинхронной платформой,
управляемой событиями, такое положение вещей обретает определен
ный смысл. Сама платформа способствует созданию программного ко
да, который делит работу на мелкие части.
В основном разделе примера видно, как реактор определяет, когда и ка
кие функции обратного вызова следует вызывать. Сначала мы создаем
фабрику клиента брокера перспективы и предписываем реактору вы
полнить подключение к порту 9876 сервера localhost, используя фаб
рику клиента PB для обработки запросов. Затем вызовом метода facto
ry.getRootObject() создается заготовка удаленного объекта. Фактиче
ски это объект отложенного действия, поэтому мы имеем возможность
объединить действия в конвейер, вызвав метод addCallback() объекта.
Первой функцией обратного вызова, которую мы добавляем, является
функция call_ls(). Функция call_ls() вызывает метод remote_ls() объ
екта отложенного действия, созданного на предыдущем шаге. Метод
callRemote() возвращает сам объект. Вторая функция обратного вызова
в цепочке обработки – это функция print_ls(). Когда реактор вызыва
ет print_ls(), она выводит результаты обращения к удаленному мето
ду remote_ls() на предыдущем шаге. Фактически реактор передает ре
зультаты вызова удаленного метода функции print_ls(). Третья функ
ция обратного вызова в цепочке – handle_err(), которая является обыч
ным обработчиком ошибок, она просто сообщает о появлении ошибок
в процессе работы. Когда в ходе выполнения возникает ошибка или
когда процесс достигает функции print_ls(), соответствующие методы
останавливают реактор.
Результат работы клиентского сценария выглядит, как показано ниже:

```
jmjones@dinkgutsy:code$ python twisted_perspective_broker_client.py
['test.log']
```

Вывод представляет собой список файлов в указанном каталоге, именно это мы и ожидали получить.
Этот сценарий выглядит несколько сложнее, чем можно было ожидать
для такого простого примера RPC. Серверный сценарий выглядит со
поставимым. Создание клиента выгдядит несколько перегруженным
изза объединения функций обратного вызова в конвейер, создания
объекта отложенного действия, реакторов и фабрик. Но это был очень
простой пример. Преимущества платформы Twisted проявляются осо
бенно ярко, когда задача, которую требуется решить, имеет более вы
сокий уровень сложности.
В примере 5.16 представлена немного модифицированная версия толь
ко что продемонстрированного клиента брокера перспективы. Вместо
удаленной функции ls он вызывает удаленную функцию ls_boom. Этот
пример демонстрирует, как производится обслуживание исключений
на стороне клиента и сервера.
```
```
Пример 5.16. Клиент брокера перспективы платформы Twisted –
обработка ошибок
#!/usr/bin/python
```
```
from twisted.spread import pb
from twisted.internet import reactor
```
```
def handle_err(reason):
print "an error occurred", reason
reactor.stop()
def call_ls(def_call_obj):
return def_call_obj.callRemote('ls_boom', '/foo')
def print_ls(print_result):
print print_result
reactor.stop()
```
```
if __name__ == '__main__':
factory = pb.PBClientFactory()
reactor.connectTCP("localhost", 9876, factory)
d = factory.getRootObject()
d.addCallback(call_ls)
d.addCallback(print_ls)
d.addErrback(handle_err)
reactor.run()
```
```
Ниже показано, что произошло, когда мы запустили этот сценарий:
jmjones@dinkgutsy:code$ python twisted_perspective_broker_client_boom.py an
error occurred [Failure instance: Traceback from remote host Traceback
unavailable
]
```
```
И на стороне сервера:
```
Peer will receive following PB traceback:
Traceback (most recent call last):
...
<большой объем диагностической информации>
...
state = method(*args, **kw)
File "twisted_perspective_broker_server.py", line 13, in remote_ls_boom
return os.listdir(directory)
exceptions.OSError: [Errno 2] No such file or directory: '/foo'
(exceptions.OSError: [Errno 2] Нет такого файла или каталога: '/foo')
```

Конкретное сообщение об ошибке появилось на стороне сервера, а не
на стороне клиента. На стороне клиента мы лишь увидели, что про
изошла какаято ошибка. Если бы Pyro или XMLRPC вели себя подоб
ным образом, мы посчитали бы, что это недостаток. Но ведь наш обра
ботчик ошибки был вызван в клиентском сценарии, реализованном на
платформе Twisted. Так как эта модель программирования (основан
ная на событиях) отличается от модели программирования, применяе
мой при использовании Pyro и XMLRPC, мы предполагаем, что обра
ботка ошибок будет производиться иначе, и программный код брокера
перспективы сделал именно то, что мы должны были ожидать от него.
Здесь мы представили вашему вниманию даже меньше, чем вершину
айсберга Twisted. На первых порах работа с платформой Twisted мо
жет оказаться достаточно сложным делом изза такой широты воз
можностей этого проекта и подходов к решению задач, так не похожих
на то, к чему привыкло большинство из нас. Платформа Twisted опре
деленно заслуживает внимательного изучения и включения ее в свой
арсенал.

### Scapy


Если вам доставляет удовольствие писать программный код для рабо
ты с сетью, вы полюбите Scapy. Scapy – это невероятно удобная инте
рактивная программа и библиотека манипулирования сетевыми паке
тами. Scapy позволяет исследовать сеть, производить сканирование,
производить трассировку маршрутов и выполнять зондирование. Бо
лее того, для Scapy имеется превосходная документация. Если вам по
нравилось это вступление, вам следует приобрести книгу, описываю
щую Scapy более подробно.
Первое, что следует отметить о Scapy, это то, что к моменту написания
этих строк данный программный продукт распространялся в виде
единственного файла. Вы можете загрузить последнюю версию Scapy
по адресу: http://hg.secdev.org/scapy/raw>file/tip/scapy.py. После это
го вы сможете запускать Scapy как самостоятельную программу или
импортировать и использовать этот продукт как библиотеку. Для на
чала воспользуемся им как интерактивной программой. Пожалуйста,
имейте в виду, что программу Scapy придется запускать с привилегиями суперпользователя root, так как ей требуется получить привилеги
рованный доступ к сетевым интерфейсам.
После того как вы загрузите и установите Scapy, вы увидите следующее:

```
Welcome to Scapy (1.2.0.2)
>>>
```

В этой программе вы можете делать все, что обычно делаете в интерак
тивной оболочке интерпретатора Python, и дополнительно в ваше рас
поряжение поступают специальные команды Scapy. Первое, что мы
сделаем, это вызовем функцию ls() в Scapy, которая выводит все дос
тупные уровни:
>>> ls()
ARP : ARP
ASN1_Packet : None
BOOTP : BOOTP
CookedLinux : cooked linux
DHCP : DHCP options
DNS : DNS
DNSQR : DNS Question Record
DNSRR : DNS Resource Record
Dot11 : 802.11
Dot11ATIM : 802.11 ATIM
Dot11AssoReq : 802.11 Association Request
Dot11AssoResp : 802.11 Association Response
Dot11Auth : 802.11 Authentication
[обрезано]
```
```
Мы обрезали вывод, потому что он слишком объемный. Ниже мы вы
полнили рекурсивный запрос DNS имени http://www.oreilly.com , использовав
общественный сервер DNS Калифорнийского политехнического уни
верситета (Caltech University):
```
```
>>> sr1(IP(dst="131.215.9.49")/UDP()/DNS(rd=1,qd=DNSQR(qname="
http://www.oreilly.com")))
Begin emission:
Finished to send 1 packets.
...*
Received 4 packets, got 1 answers, remaining 0 packets
IP version=4L ihl=5L tos=0x0 len=223 id=59364 flags=DF
frag=0L ttl=239 proto=udp chksum=0xb1e src=131.215.9.49 dst=10.0.1.3
options=''
|UDP sport=domain dport=domain len=203 chksum=0x843 |
DNS id=0 qr=1L opcode=QUERY aa=0L tc=0L rd=1L ra=1L z=0L
rcode=ok qdcount=1 ancount=2 nscount=4 arcount=3 qd=
DNSQR qname='www.oreilly.com.' qtype=A qclass=IN |>
an=DNSRR rrname='www.oreilly.com.' type=A rclass=IN ttl=21600
rdata='208.201.239.36'
[обрезано]
Затем выполнили трассировку маршрута:
>>> ans,unans=sr(IP(dst="oreilly.com",
>>> ttl=(4,25),id=RandShort())/TCP(flags=0x2))
Begin emission:
..............*Finished to send 22 packets.
*...........*********.***.***.*.*.*.*.*
Received 54 packets, got 22 answers, remaining 0 packets
>>> for snd, rcv in ans:
... print snd.ttl, rcv.src, isinstance(rcv.payload, TCP)
...
[обрезано]
20 208.201.239.37 True
21 208.201.239.37 True
22 208.201.239.37 True
23 208.201.239.37 True
24 208.201.239.37 True
25 208.201.239.37 True
```
```
Программе Scapy также под силу воспроизводить содержимое паке
тов, на манер утилиты tcpdump:
>>> sniff(iface="en0", prn=lambda x: x.show())
###[ Ethernet ]###
dst= ff:ff:ff:ff:ff:ff
src= 00:16:cb:07:e4:58
type= IPv4
###[ IP ]###
version= 4L
ihl= 5L
tos= 0x0
len= 78
id= 27957
flags=
frag= 0L
ttl= 64
proto= udp
chksum= 0xf668
src= 10.0.1.3
dst= 10.0.1.255
options= ''
[обрезано]
```
```
Кроме того, возможно реализовать трассировку маршрутов в графиче
ском режиме, если в системе установлены graphviz и imagemagic. Сле
дующий пример взят из официальной документации к Scapy:
```
```
>>> res,unans = traceroute(["www.microsoft.com","www.cisco.com",
"www.yahoo.com","www.wanadoo.fr","www.pacsec.com"
],dport=[80,443],maxttl=20,
retry=2)
Begin emission:
************************************************************************
Finished to send 200 packets.
******************Begin emission:
*******************************************Finished to send 110 packets.
**************************************************************Begin emission:
Finished to send 5 packets.
Begin emission:
Finished to send 5 packets.
Received 195 packets, got 195 answers, remaining 5 packets
193.252.122.103:tcp443 193.252.122.103:tcp80 198.133.219.25:tcp443
198.133.219.25:tcp80 207.46.193.254:tcp443 207.46.193.254:tcp80
69.147.114.210:tcp443 69.147.114.210:tcp80 72.9.236.58:tcp443
72.9.236.58:tcp80
```
```
Теперь из полученных результатов можно создать неплохой график:
>>> res.graph()
>>> res.graph(type="ps",target="| lp")
>>> res.graph(target="> /tmp/graph.svg")
```
```
Теперь, если у вас в системе установлены graphviz и imagemagic, вы
будете поражены красотой графической визуализации!
Однако истинная прелесть Scapy проявляется при создании своих соб
ственных инструментов командной строки и сценариев. В следующем
разделе мы посмотрим на Scapy как на библиотеку.

### Создание сценариев с использованием Scapy.


Теперь, когда с помощью Scapy мы можем создавать нечто существен
ное, мы покажем реализацию такого интересного инструмента, как
arping. Сначала рассмотрим платформозависимую реализацию инст
румента arping:
```
```
#!/usr/bin/env python
import subprocess
import re
import sys
```
```
def arping(ipaddress="10.0.1.1"):
"""Функция arping принимает IPадрес хоста или сети,
возвращает вложенный список адресов mac/ip"""
#Предполагается, что arping используется в Red Hat Linux
p = subprocess.Popen("/usr/sbin/arpingc 2 %s" % ipaddress, shell=True,
stdout=subprocess.PIPE)
out = p.stdout.read()
result = out.split()
#pattern = re.compile(":")
for item in result:
if ':' in item:
print item
if __name__ == '__main__':
if len(sys.argv) > 1:
for ip in sys.argv[1:]:
print "arping", ip
arping(ip)
else:
arping()
```
```
А теперь посмотрим, как с помощью Scapy можно реализовать то же
самое, но платформонезависимым способом:
#!/usr/bin/env python
from scapy import srp,Ether,ARP,conf
import sys
```
```
def arping(iprange="10.0.1.0/24"):
"""Функция arping принимает IPадрес хоста или сети,
возвращает вложенный список адресов mac/ip"""
conf.verb=0
ans,unans=srp(Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst=iprange),
timeout=2)
```
```
collection = []
for snd, rcv in ans:
result = rcv.sprintf(r"%ARP.psrc% %Ether.src%").split()
collection.append(result)
return collection
if __name__ == '__main__':
if len(sys.argv) > 1:
for ip in sys.argv[1:]:
print "arping", ip
print arping(ip)
else:
print arping()
```
```
Результатом работы сценария является весьма полезная информация,
которая содержит MAC и IPадреса всех узлов подсети:
```
```
# sudo python scapy_arp.py
[['10.0.1.1', '00:00:00:00:00:10'], ['10.0.1.7', '00:00:00:00:00:12'],
['10.0.1.30', '00:00:00:00:00:11'], ['10.0.1.200', '00:00:00:00:00:13']]
```
```
Эти примеры позволяют получить представление о том, насколько
простой и удобной в использовании является Scapy.
```
