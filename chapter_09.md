## Глава 9. Управление пакетами

**Введение**

Управление пакетами является одной из наиболее важных составляю
щих успешного проектирования программного обеспечения. Управле
ние пакетами в разработке аналогично организации доставки в компа
ниях, занимающихся электронной торговлей, таких как Amazon. Ес
ли бы не было компаний, выполняющих доставку, то и существование
самой компании Amazon было бы невозможным. Точно так же, если
не будет простой, устойчивой и функциональной системы управления
пакетами для операционной системы или языка, то и разработка про
граммного обеспечения будет сталкиваться с определенными ограни
чениями.
Когда упоминается «управление пакетами», возможно, первое, о чем
вы вспоминаете, это о пакетах _.rpm_ и утилите yum или о пакетах _.deb_
и утилите apt, или о какихто других комплексах управления пакета
ми уровня операционной системы. Мы рассмотрим все это в данной
главе, но основное наше внимание будет уделено созданию и управле
нию пакетами модулей на языке Python и среде окружения языка Py
thon. При использовании Python всегда имелась возможность обеспе
чить доступ к программному коду на языке Python для всей системы.
Кроме того, недавно появилось несколько проектов, которые еще боль
ше улучшили гибкость, удобство и простоту создания пакетов с моду
лями на языке Python, управления ими и распространения.

```
Среди этих проектов можно назвать setuptools, Buildout и virtualenv.
Часто эти проекты используют в процессе разработки и для управле
ния средой разработки. Но по большей части они предназначены для
обеспечения развертывания программного кода на языке Python плат
форменнонезависимым способом. (Обратите внимание на оговорку
«по большей части».)
```

**314** Глава 9. Управление пакетами

```
Другой способ развертывания связан с созданием системнозависимых
пакетов и передачей их на компьютеры конечных пользователей. В не
которых случаях это два совершенно независимых подхода, хотя в них
имеется и чтото общее. В этой главе мы будем рассматривать свободно
распространяемый инструмент EPM, который способен создавать па
кеты для платформ AIX, Debian/Ubuntu, FreeBSD, HPUX, IRIX, Mac
OS X, NetBSD, OpenBSD, Red Hat, Slackware, Solaris и Tru64 Unix.
Знание принципов управления пакетами необходимо не только раз
работчикам программного обеспечения. Это совершенно необходимо
и системным администраторам. На практике нередко системный ад
министратор является тем человеком, на которого возлагаются задачи
управления пакетами. Понимание новейших приемов управления па
кетами для языка Python и для различных операционных систем яв
ляется одним из способов повысить свою ценность как специалиста.
Хотелось бы надеяться, что данная глава поможет вам в этом. Кроме
того, ценную информацию по темам, которые мы затронем здесь, мож
но найти по адресу http://wiki.python.org/moin/buildout/pycon2008_
tutorial.
```
**Setuptools и пакеты Python Eggs**

```
Согласно официальной документации «setuptools – это набор расши
рений к distutils языка Python (на большинстве платформ – для Py
thon 2.3.5, однако для 64битовых платформ требуется версия не ниже
Python 2.4), которые упрощают сборку и распространение пакетов,
особенно когда они имеют зависимости от других пакетов».
До появления setuptools комплект distutils был основным средством
создания установочных пакетов с модулями на языке Python. setup
tools – это библиотека, которая расширяет возможности distutils. На
звание «eggs» относится к окончательному комплекту пакетов и моду
лей на языке Python, напоминая файлы .rpm или .deb. Как правило,
они распространяются в формате архива ZIP и устанавливаются либо
в сжатом виде, либо распаковываются, чтобы иметь возможность пе
ремещаться по содержимому пакета. Создание пакетов «eggs» – это
особенность библиотеки setuptools, которая работает с easy_install.
Согласно официальной документации «Easy Install – это модуль на
языке Python (easy_install), связанный с библиотекой setuptools, ко
торая позволяет автоматически загружать, собирать, устанавливать
и управлять пакетами языка Python». Несмотря на то, что это модуль,
чаще его воспринимают и используют как инструмент командной
строки. В этом разделе мы расскажем о setuptools, easy_install и eggs
и разъясним, для чего каждый из этих инструментов используется.
В этой главе мы выделим наиболее полезные на наш взгляд особенности
setuptools и easy_install. Чтобы получить полный комплект документа
ции к ним, обращайтесь по адресам http://peak.telecommunity.com/
```

Использование easy_install **315**

```
DevCenter/setuptools и http://peak.telecommunity.com/DevCenter/Easy>
Install , соответственно.
Сложные инструменты, способные делать удивительные вещи, часто
бывает сложно понять. Инструмент setuptools сложно понять отчасти
потому, что он делает именно удивительные вещи. С помощью этого
раздела, который можно рассматривать как краткое руководство,
и с последующим изучением руководств вы получите возможность
научиться использовать setuptools, easy_install и пакеты Python как
пользователь и как разработчик.
```
**Использование easy_install**

```
Основные принципы использования easy_install понять очень легко.
Большинство читателей этой книги наверняка использовали rpm,
yum, aptget, fink или подобные им инструменты управления пакета
ми. Фраза «Easy Install» (простая установка) часто означает использо
вание инструмента командной строки с именем easy_install для вы
полнения задач, похожих на выполняемые утилитой yum в системах на
базе Red Hat или apt–get в системах на базе Debian, – для пакетов Py
thon.
Инструмент easy_install можно установить с помощью запуска сцена
рия «начальной установки» с именем ez_setup.py для версии Python,
с которой будет работать easy_install. Сценарий ez_setup.py загрузит
последнюю версию setuptools и автоматически установит easy_install
как сценарий в местоположение по умолчанию, которое в UNIXпо
добных системах обычно соответствует каталогу, где находится испол
няемый файл интерпретатора python. Давайте посмотрим, насколько
это «просто» в действительности. Взгляните на пример 9.1.
```
```
Пример 9.1. Загрузка и установка easy_install
$ curl http://peak.telecommunity.com/dist/ez_setup.py
> ez_setup.py
% Total % Received % Xferd Average Speed Time Time Time Current
Dload Upload Total Spent Left Speed
100 9419 100 9419 0 0 606 0 0:00:15 0:00:15:: 83353
$ ls
ez_setup.py
$ sudo python2.5 ez_setup.py
Password:
Searching for setuptools
Reading http://pypi.python.org/simple/setuptools/
Best match: setuptools 0.6c8
Processing setuptools0.6c8py2.5.egg
setuptools 0.6c8 is already the active version in easyinstall.pth
Installing easy_install script to /usr/local/bin
Installing easy_install2.5 script to /usr/local/bin
```

**316** Глава 9. Управление пакетами

```
Using /Library/Python/2.5/sitepackages/setuptools0.6c8py2.5.egg
Processing dependencies for setuptools
Finished processing dependencies for setuptools
$
```
```
В этом случае сценарий easy_install был помещен в каталог /usr/local/
bin под двумя различными именами.
```
```
$ lsl /usr/local/bin/easy_install*
rwxrxrx 1 root wheel 364 Mar 9 18:14 /usr/local/bin/easy_install
rwxrxrx 1 root wheel 372 Mar 9 18:14 /usr/local/bin/easy_install2.5
```
```
В соответствии с соглашениями, продолжительное время существую
щими в языке Python, при установке выполняемых файлов программ
один файл устанавливается под именем, содержащим номер версии
Python, и один – без номера версии. Это означает, что по умолчанию
будет использоваться файл, имя которого не содержит номер версии,
пока пользователь явно не укажет имя файла с номером версии. Это
также означает, что по умолчанию будет использоваться последняя
установленная версия. Это удобно еще и потому, что старая версия по
прежнему остается в файловой системе.
Ниже приводится содержимое вновь установленного файла /usr/local/
bin/easy_install :
```
```
#!/System/Library/Frameworks/Python.framework/Versions/2.5/Resources/
Python.app/Contents/MacOS/Python
# EASYINSTALLENTRYSCRIPT:
'setuptools==0.6c8','console_scripts','easy_install'
__requires__ = 'setuptools==0.6c8'
import sys
from pkg_resources import load_entry_point
sys.exit(
load_entry_point('setuptools==0.6c8', 'console_scripts', 'easy_install')()
)
```
```
Главное здесь то, что при установке setuptools устанавливается сцена
рий с именем easy_install, который может использоваться для уста
новки и управления программным кодом на языке Python. Второй по
важности момент, ради которого мы привели содержимое сценария
easy_install, заключается в том, что он относится к типу сценариев,
которые создаются автоматически при использовании «точек входа»,
когда определяются пакеты. Пока не надо беспокоиться о содержимом
этого сценария, о точках входа или о создании таких сценариев, как
этот. Мы подойдем к этой теме далее в этой главе.
Теперь, когда в нашем распоряжении имеется сценарий easy_install,
мы можем установить любой пакет, находящийся в центральном репо
зитарии модулей Python, который обычно называют PyPI (Python
```

Использование easy_install **317**

```
Package Index – каталог пакетов Python), или «Cheesshop»: http://py>
pi.python.org/pypi.
Чтобы установить IPython, оболочку, которая используется для де
монстрации примеров на протяжении всей книги, можно просто за
пустить следующую команду:
sudo easy_install ipython
```
```
Обратите внимание, что для выполнения своей работы сценарий
easy_install требует в данном случае привилегий суперпользователя,
так как пакеты устанавливаются в глобальный для Python каталог
site>packages. Он также помещает сценарии в каталог, по умолчанию
предназначенный операционной системой для сценариев, который
обычно является каталогом, где находится исполняемый файл python.
Для установки пакетов с помощью easy_install необходимо обладать
правом на запись в каталог site>packages и в каталог, куда был установ
лен Python. Если у вас это вызывает затруднения, обратитесь к разде
лу главы, где обсуждается использование virtualenv и setuptools. Как
вариант, можно было бы скомпилировать и установить Python в ката
лог по своему выбору: например, в свой домашний каталог.
Прежде чем мы перейдем к изучению дополнительных возможностей
инструмента easy_install, коротко вспомним основные моменты ис
пользования easy_install:
```
1. Загрузить сценарий начальной установки ez_setup.py.
2. Запустить ez_setup.py для версии Python, с которой будет работать
    easy_install.
3. Если в вашей системе установлено несколько версий Python, явно
    запускайте сценарий easy_install с требуемым номером версии.

```
ПОРТ РЕТ ЗНА МЕ НИ ТО СТИ: EASY INSTALL
```
**Филлип Дж. Эби (Phillip J. Eby)**

```
Филлип Дж. Эби отвечает за систему Python En
hancement Proposals (система приема предложе
ний по улучшению Python), поддержку стандарта
WSGI (Web Server Gateway Interface – интерфейс
взаимодействия с вебсервером), setuptools и мно
гое другое. О нем рассказывалось в книге «Dream
ing in Code» (Three Rivers Press). Вы можете посе
тить его блог, посвященный программированию: http://dirtsim>
ple.org/programming/.
```

**318** Глава 9. Управление пакетами

**Дополнительные особенности easy_install**

```
Для большинства тех, кто пользуется сценарием easy_install, вполне
достаточно вызывать его с единственным аргументом командной стро
ки, без дополнительных параметров. (К слову сказать, при использо
вании easy_install с единственным аргументом – именем пакета, этот
сценарий просто загрузит и установит этот пакет, как показано в пре
дыдущем примере с IPython.) Тем не менее, иногда бывают случаи, ко
гда неплохо было бы иметь возможности для выполнения дополни
тельных действий, помимо загрузки пакета из Python Package Index.
К счастью, easy_install имеет в запасе несколько оригинальных реше
ний и обладает достаточно высокой гибкостью, чтобы предоставить це
лый набор дополнительных возможностей.
```
**Поиск пакетов на вебстраницах**

```
Как было показано ранее, easy_install может отыскивать пакеты в цен
тральном репозитарии и автоматически устанавливать их. Но кроме
этого он имеет возможность устанавливать пакеты любыми другими
способами, которые только можно себе представить. Ниже приводится
пример того, как выполнить поиск пакета на вебстранице и устано
вить или обновить пакет по имени и номеру версии:
$ easy_installf http://code.google.com/p/liten/ liten
Searching for liten
Reading http://code.google.com/p/liten/
Best match: liten 0.1.3
Downloading http://liten.googlecode.com/files/liten0.1.3py2.4.egg
[обрезано]
```
```
В данной ситуации на странице http://code.google.com/p/liten/ имеет
ся пакет .egg для Python 2.4 и Python 2.5. Ключ –f сценария easy_in
stall определяет адрес страницы, где требуется отыскать пакет. Сце
нарий отыскал оба пакета и установил версию для Python 2.4, как наи
более соответствующую. Достаточно очевидно, что это очень мощная
особенность, так как easy_install не только отыскал ссылку на пакет,
но и обнаружил наиболее подходящую версию.
```
**Установка дистрибутива с исходными**

**текстами по заданному URL**

```
Теперь мы попробуем автоматически установить дистрибутив с исход
ными текстами по известному адресу URL:
```
```
% easy_install http://superbwest.dl.sourceforge.net/sourceforge
/sqlalchemy/SQLAlchemy0.4.3.tar.gz
```
```
Downloading http://superbwest.dl.sourceforge.net/sourceforge
/sqlalchemy/SQLAlchemy0.4.3.tar.gz
Processing SQLAlchemy0.4.3.tar.gz
```

Дополнительные особенности easy_install **319**

```
Running SQLAlchemy0.4.3/setup.pyq bdist_eggdistdir
/var/folders/LZ/LZFo5h8JEW4Jzr+ydkXfI++++TI/Tmp/
easy_installGw2Xq3/SQLAlchemy0.4.3/eggdisttmpMf4jir
zip_safe flag not set; analyzing archive contents...
sqlalchemy.util: module MAY be using inspect.stack
sqlalchemy.databases.mysql: module MAY be using inspect.stack
Adding SQLAlchemy 0.4.3 to easyinstall.pth file
Installed /Users/ngift/src/py24ENV/lib/python2.4/sitepackages/SQLAlchemy
0.4.3py2.4.egg
Processing dependencies for SQLAlchemy==0.4.3
Finished processing dependencies for SQLAlchemy==0.4.3
```
```
Мы передали сценарию easy_install адрес местоположения сжатого
тарболла. Он обнаружил, что должен установить дистрибутив с исход
ными текстами, причем нам не пришлось явно сообщать ему об этом.
Это очень интересный способ установки, но, чтобы он работал, в корне
вом каталоге дистрибутива должен иметься файл setup.py. Например,
к моменту написания этих строк, если разработчик создаст несколько
уровней вложенности каталогов, попытка выполнить установку тако
го пакета потерпит неудачу.
```
**Установка пакетов, расположенных в локальной**

**или в сетевой файловой системе**

```
Ниже приводится пример установки пакета .egg , находящегося в ло
кальной файловой системе или на смонтированном томе NFS:
easy_install /net/src/eggs/convertWindowsToMacOperatingSystempy2.5.egg
```
```
Кроме всего прочего существует возможность устанавливать пакеты,
находящиеся в смонтированном каталоге NFS или в локальном разде
ле. Это может быть очень удобно при распространении пакетов в окру
жении *nix, особенно когда имеется несколько машин, которые долж
ны быть синхронизированы друг с другом по версиям программного
кода, работающего на них. Некоторые другие сценарии, представлен
ные в этой книге, могли бы помочь в создании демона, выполняющего
опрос. Каждый клиент мог бы с помощью такого демона проверять на
личие обновлений в центральном репозитарии пакетов. При обнару
жении новой версии он мог бы автоматически выполнять обновление.
```
**Обновление пакетов**

```
Еще одна область применения easy_install – обновление пакетов. В сле
дующих нескольких примерах демонстрируется установка и обновле
ние пакета CherryPy.
Сначала устанавливается версия CherryPy 2.2.1:
$ easy_install cherrypy==2.2.1
Searching for cherrypy==2.2.1
Reading http://pypi.python.org/simple/cherrypy/
```

**320** Глава 9. Управление пакетами

```
....
Best match: CherryPy 2.2.1
Downloading http://download.cherrypy.org/cherrypy/2.2.1/CherryPy2.2.1.tar.gz
....
Processing dependencies for cherrypy==2.2.1
Finished processing dependencies for cherrypy==2.2.1
```
```
Теперь посмотрим, что произойдет, если предложить сценарию easy_in
stall попытаться установить пакет, который уже был установлен:
```
```
$ easy_install cherrypy
Searching for cherrypy
Best match: CherryPy 2.2.1
Processing CherryPy2.2.1py2.5.egg
CherryPy 2.2.1 is already the active version in easyinstall.pth
Using /Users/jmjones/python/cherrypy/lib/python2.5/sitepackages/CherryPy
2.2.1py2.5.egg
Processing dependencies for cherrypy
Finished processing dependencies for cherrypy
```
```
После установки некоторой версии пакета можно обновить его до бо
лее свежей версии, явно указав, какую версию нужно загрузить и ус
тановить:
```
```
$ easy_install cherrypy==2.3.0 Searching for
cherrypy==2.3.0
Reading http://pypi.python.org/simple/cherrypy/
....
Best match: CherryPy 2.3.0
Downloading http://download.cherrypy.org/cherrypy/2.3.0/CherryPy2.3.0.zip
....
Processing dependencies for cherrypy==2.3.0
Finished processing dependencies for cherrypy==2.3.0
```
```
Обратите внимание: в данном примере мы не использовали ключ
––upgrade. В действительности этот ключ необходим, только если у вас
уже установлена некоторая версия пакета и вы хотите обновить его до
самой последней версии.
Далее мы обновляем пакет до версии CherryPy 3.0.0, используя ключ
––upgrade. В данном случае использовать ключ ––upgrade было совер
шенно необязательно:
```
```
$ easy_installupgrade cherrypy==3.0.0
Searching for cherrypy==3.0.0
Reading http://pypi.python.org/simple/cherrypy/
....
Best match: CherryPy 3.0.0
Downloading http://download.cherrypy.org/cherrypy/3.0.0/CherryPy3.0.0.zip
....
Processing dependencies for cherrypy==3.0.0
Finished processing dependencies for cherrypy==3.0.0
```

Дополнительные особенности easy_install **321**

```
Если использовать ключ ––upgrade, не указывая номер версии, обновле
ние будет выполнено до самой последней версии пакета. Обратите вни
мание: действие команды в этом случае отличается от действия коман
ды easy_install cherrypy. Команда easy_install cherrypy обнаружит, что
ранее уже была установлена некоторая версия пакета и поэтому ника
ких действий предпринимать не будет. В следующем примере про
изойдет обновление пакета CherryPy до самой последней версии:
$ easy_installupgrade cherrypy
Searching for cherrypy
Reading http://pypi.python.org/simple/cherrypy/
....
Best match: CherryPy 3.1.0beta3
Downloading http://download.cherrypy.org/cherrypy/3.1.0beta3/CherryPy
3.1.0beta3.zip
....
Processing dependencies for cherrypy
Finished processing dependencies for cherrypy
```
```
Теперь у нас установлена версия CherryPy 3.1.0b3. Если теперь попро
бовать выполнить обновление до версии, больше чем 3.0.0, никаких
действий предприниматься не будет, так как у нас уже установлена та
кая версия:
$ easy_installupgrade cherrypy>3.0.0
$
```
**Установка распакованного дистрибутива с исходными**

**текстами в текущем рабочем каталоге**

```
Несмотря на всю свою тривиальность, такой способ установки может
оказаться полезным. Вместо того чтобы следовать через процедуру py
thon setup.py install, вы можете просто ввести следующую команду
(для этого требуется меньше вводить с клавиатуры, поэтому это будет
ценный совет для ленивых):
```
```
easy_install
```
**Извлечение дистрибутива с исходными текстами**

**в заданный каталог**

```
Следующий пример может использоваться для поиска дистрибутива
с исходными текстами или пакета по указанному URL с последующей
распаковкой его в заданный каталог:
```
```
easy_installeditablebuilddirectory ~/sandbox liten
```
```
Это достаточно удобный прием, так как он позволяет с помощью
easy_install поместить дистрибутив с исходными текстами в требуемый
каталог. Так как в процессе установки пакета с помощью сценария
easy_install не всегда устанавливается все его содержимое (например,
```

**322** Глава 9. Управление пакетами

```
документация или примеры программного кода), это отличный способ
узнать, что входит в состав дистрибутива. В этом случае easy_install
только лишь скопирует файлы из дистрибутива. Если вам потребуется
установить пакет, вам нужно будет запустить сценарий easy_install
еще раз.
```
**Изменение активной версии пакета**

```
В этом примере предполагается, что у вас уже установлен пакет liten
версии 0.1.3 и при этом была установлена некоторая другая версия
liten. Кроме того, предполагается, что «активной» является эта дру
гая версия. Ниже показано, как можно активировать версию:
```
```
easy_install liten=0.1.3
```
```
Этот прием работает как в случае перехода к использованию более ста
рой версии, так и в случае возврата к более новой версии пакета.
```
**Преобразование отдельного файла .py в пакет .egg**

```
Ниже показано, как преобразовать обычный пакет Python в пакет .egg
(обратите внимание на ключ –f):
```
```
easy_installf "http://svn.colorstudy.com/virtualenv/
trunk/virtualenv.py#egg=virtualenv1.0" virtualenv
```
```
Этот прием пригодится, когда необходимо упаковать единственный
файл .py в пакет .egg. Иногда этот метод может оказаться лучшим спо
собом, когда необходимо обеспечить доступ к ранее распакованному
отдельному файлу из любой точки файловой системы. Как вариант,
можно было бы добавить путь к требуемому файлу в переменную окру
жения PYTHONPATH. В этом примере мы получаем из основного каталога
проекта сценарий virtualenv.py, упаковываем его и указываем нашу
собственную версию и метку к ней. В строке URL подстрока #egg=vir
tualenv–1.0 просто определяет имя пакета и номер версии, выбранные
нами для этого сценария. Аргумент, который следует за строкой URL,
определяет имя создаваемого пакета. В этом аргументе желательно ис
пользовать имена, которые не противоречили бы строке URL, потому
что мы предписываем easy_install установить пакет с тем же именем,
что и созданный. Даже при том, что было бы желательно указывать
непротиворечивые имена, вы не должны чувствовать себя обязанными
сохранять название пакета в соответствии с названием модуля. На
пример:
easy_installf "http://svn.colorstudy.com/virtualenv/
trunk/virtualenv.py#egg=foofoo1.0" foofoo
```
```
Эта команда делает в точности то же самое, что и предыдущий пример,
только в этом случае создается пакет с именем foofoo, а не virtualenv.
Какое имя вы выберете для своего пакета – полностью ваше дело.
```

Дополнительные особенности easy_install **323**

**Аутентификация на сайтах, доступ к которым**

**защищен паролем**

```
В практике может возникнуть ситуация, когда вам потребуется уста
новить пакет .egg с вебсайта, где требуется пройти аутентификацию,
прежде чем будет разрешено загружать с него какиелибо файлы. В та
ком случае можно указать имя пользователя и пароль прямо в строке
URL, как показано ниже:
easy_installf http://uid:passwd@example.com/packages
```
```
Вы можете заниматься на работе своим собственным проектом, и вам
не хотелось бы, чтобы ваши коллеги узнали о нем. (Разве не все занима
ются этим?) Один из способов передать свои пакеты коллегам «изпод
полы» заключается в том, чтобы создать простой файл .htaccess и за
тем выполнять процедуру аутентификации в сценарии easy_install.
```
### Интеграция конфигурационных файлов

```
Для опытных пользователей в арсенале easy_install имеется еще один
прием. Значения параметров по умолчанию можно задать с помощью
конфигурационного файла, который имеет формат iniфайлов. Для
системного администратора это особенно удачная возможность, так
как позволяет определять настройки клиентов, использующих сцена
рий easy_install. Параметры конфигурации будут отыскиваться сце
нарием easy_install в следующих файлах и в следующем порядке: те>
кущий_рабочий_каталог/setup.cfg , ~/.pydistutils.cfg и в файле distu>
tils.cfg , в каталоге пакета distutils.
Что можно добавить в этот конфигурационный файл? Обычно здесь
определяются два параметра: сайт(ы) в локальной сети, откуда можно
загружать пакеты, и нестандартный каталог установки пакетов. Ниже
показано, как может выглядеть файл конфигурации для easy_install:
[easy_install]
```
```
#Где искать пакеты
find_links = http://code.example.com/downloads
```
```
#Ограничить возможности поиска этими доменами
allow_hosts = *.example.com
```
```
#Куда устанавливать пакеты. Обратите внимание: этот каталог должен
#находиться в PYTHONPATH
install_dir = /src/lib/python
```
```
В этом конфигурационном файле, который мы могли бы назвать, на
пример, ~/.pydistutils.cfg , определяется адрес URL для поиска паке
тов, разрешается искать пакеты только в домене example.com (и в под
доменах) и, наконец, указывается, куда должны помещаться пакеты
при установке.
```

**324** Глава 9. Управление пакетами

**Коротко о дополнительных особенностях easy_install**

```
Этот раздел не может служить заменой всеобъемлющей официальной
документации с описанием сценария easy_install, его цель состояла
лишь в том, чтобы обозначить некоторые основные особенности, кото
рые могут использоваться опытными пользователями. Сценарий
easy_install продолжает активно разрабатываться, поэтому будет не
лишним чаще обращаться на страницу http://peak.telecommunity.com/
DevCenter/EasyInstall за обновлениями в документации. Кроме того,
существует почтовая рассылка, она называется distutilssig (где sig про
исходит от special interest group – группа с особыми интересами), где
обсуждаются все проблемы, связанные с распространением программ
ного кода. Подпишитесь на рассылку по адресу http://mail.python.org/
mailman/listinfo/distutils>sig , и вы сможете посылать свои сообщения
об обнаруженных ошибках и получать помощь, касающуюся исполь
зования easy_install.
Наконец, выполнив команду easy_install ––help, вы обнаружите еще
большее число параметров, о которых мы даже не упоминали здесь.
Весьма вероятно, что какаялибо особенность, которая вам необходи
ма, уже реализована в easy_install.
```
**Создание пакетов**

```
Ранее мы уже упоминали, что пакеты с расширением .egg – это пакеты
модулей на языке Python, но мы не давали определения пакетам луч
ше, чем это. Ниже приводится определение «пакета egg», взятое с веб
сайта проекта setuptools:
```
```
Пакеты в формате .egg – это предпочтительный двоичный формат ди
стрибутивов для EasyInstall, потому что являются кроссплатфор
менными (для пакетов с модулями исключительно на языке Python),
могут импортироваться непосредственно и содержат метаданные
проекта, включая сценарии и информацию о зависимостях проекта.
Они могут просто загружаться и добавляться в значение атрибута
sys.path или помещаться в каталог, который уже имеется в sys.path,
после чего они автоматически будут обнаруживаться системой
управления пакетами во время выполнения.
```
```
Мы не приводили причины, по которым системный администратор
мог бы быть заинтересован в создании пакетов. Если ваша деятель
ность ограничивается созданием одноразовых сценариев, эти знания
не окажутся вам особенно полезными. Но когда вы начнете выделять
общие шаблоны и задачи, вы обнаружите, что пакеты помогут вам из
бежать многих неприятностей. Если вы создадите небольшую библио
теку сценариев, предназначенных для решения наиболее часто встре
чающихся задач, вы сможете оформить ее в виде пакета. А если вы
сделаете это, то вы не только сэкономите время на написании про
```

Создание пакетов **325**

```
граммного кода, но облегчите себе процедуру их установки на несколь
ких машинах.
Создание пакетов Python представляет собой чрезвычайно простой
процесс, состоящий из четырех этапов:
```
1. Установить setuptools.
2. Создать файлы, которые необходимо поместить в пакет.
3. Создать файл _setup.py_.
4. Запустить.
    python setup.py bdist_egg

```
Инструмент setuptools у нас уже установлен, поэтому мы можем дви
нуться дальше и создать файлы для помещения в пакет:
$ cd /tmp
$ mkdir eggexample
$ cd eggexample
$ touch helloegg.py
```
```
В данном случае пакет будет содержать пустой модуль на языке Py
thon с именем hello–egg.py.
Далее создадим простейший файл setup.py :
```
```
from setuptools import setup, find_packages
setup(
name = "HelloWorld",
version = "0.1",
packages = find_packages(),
)
```
```
Теперь создадим пакет:
$ python setup.py bdist_egg
running bdist_egg
running egg_info
creating HelloWorld.egginfo
writing HelloWorld.egginfo/PKGINFO
writing toplevel names to HelloWorld.egginfo/top_level.txt
writing dependency_links to HelloWorld.egginfo/dependency_links.txt
writing manifest file 'HelloWorld.egginfo/SOURCES.txt'
reading manifest file 'HelloWorld.egginfo/SOURCES.txt'
writing manifest file 'HelloWorld.egginfo/SOURCES.txt'
installing library code to build/bdist.macosx10.5i386/egg
running install_lib
warning: install_lib: 'build/lib' does not exist no Python modules to
install
creating build
creating build/bdist.macosx10.5i386
creating build/bdist.macosx10.5i386/egg
creating build/bdist.macosx10.5i386/egg/EGGINFO
```

**326** Глава 9. Управление пакетами

```
copying HelloWorld.egginfo/PKGINFO> build/bdist.macosx10.5i386/egg/
EGGINFO
copying HelloWorld.egginfo/SOURCES.txt> build/bdist.macosx10.5i386/egg/
EGGINFO
copying HelloWorld.egginfo/dependency_links.txt> build/bdist.macosx10.5
i386/egg/EGGINFO
copying HelloWorld.egginfo/top_level.txt> build/bdist.macosx10.5i386/
egg/EGGINFO
zip_safe flag not set; analyzing archive contents...
creating dist
creating 'dist/HelloWorld0.1py2.5.egg' and adding 'build/bdist.macosx
10.5i386/egg' to it
removing 'build/bdist.macosx10.5i386/egg' (and everything under it)
$ ll
total 8
drwxrxrx 6 ngift wheel 204 Mar 10 06:53 HelloWorld.egginfo
drwxrxrx 3 ngift wheel 102 Mar 10 06:53 build
drwxrxrx 3 ngift wheel 102 Mar 10 06:53 dist
rwrr 1 ngift wheel 0 Mar 10 06:50 helloegg.py
rwrr 1 ngift wheel 131 Mar 10 06:52 setup.py
```
```
Установим пакет:
$ sudo easy_install HelloWorld0.1py2.5.egg
sudo easy_install HelloWorld0.1py2.5.egg
Password:
Processing HelloWorld0.1py2.5.egg
Removing /Library/Python/2.5/sitepackages/HelloWorld0.1py2.5.egg
Copying HelloWorld0.1py2.5.egg to /Library/Python/2.5/sitepackages
Adding HelloWorld 0.1 to easyinstall.pth file
```
```
Installed /Library/Python/2.5/sitepackages/HelloWorld0.1py2.5.egg
Processing dependencies for HelloWorld==0.1
Finished processing dependencies for HelloWorld==0.1
```
```
Как видите, пакеты создаются чрезвычайно просто. Однако созданный
пакет в действительности был пустым файлом, поэтому мы создадим
другой сценарий на языке Python и рассмотрим процедуру создания
пакета более подробно.
Ниже приводится очень простой сценарий на языке Python, который
отображает файлы в каталоге, являющиеся символическими ссылками,
показывает местоположение, где находятся соответствующие им на
стоящие файлы, и выясняет, существуют ли эти файлы на самом деле:
#!/usr/bin/env python
```
```
import os
import sys
```
```
def get_dir_tuple(filename, directory):
abspath = os.path.join(directory, filename)
realpath = os.path.realpath(abspath)
exists = os.path.exists(abspath)
```

Создание пакетов **327**

```
return (filename, realpath, exists)
def get_links(directory):
file_list = [get_dir_tuple(f, directory) for f in os.listdir(directory)
if os.path.islink(os.path.join(directory, f))]
return file_list
def main():
if not len(sys.argv) == 2:
print 'USAGE: %s directory' % sys.argv[0]
sys.exit(1)
directory = sys.argv[1]
print get_links(directory)
if __name__ == '__main__':
main()
```
```
Затем мы создадим сценарий setup.py, который будет использоваться
инструментом setuptools. Это еще один минимально возможный файл
setup.py , как и в предыдущем примере:
```
```
from setuptools import setup, find_packages
setup(
name = "symlinkator",
version = "0.1",
packages = find_packages(),
entry_points = {
'console_scripts': [
'linkator = symlinkator.symlinkator:main',
],
},
)
```
```
Здесь объявляется имя пакета – «symlinkator», номер версии 0.1 и ука
зывается, что инструмент setuptools будет пытаться отыскать любые
подходящие файлы для включения. Раздел entry_points пока просто
игнорируйте.
Теперь соберем пакет, запустив команду python setup.py bdist_egg:
```
```
$ python setup.py bdist_egg
running bdist_egg
running egg_info
creating symlinkator.egginfo
writing symlinkator.egginfo/PKGINFO
writing toplevel names to symlinkator.egginfo/top_level.txt
writing dependency_links to symlinkator.egginfo/dependency_links.txt
writing manifest file 'symlinkator.egginfo/SOURCES.txt'
writing manifest file 'symlinkator.egginfo/SOURCES.txt'
installing library code to build/bdist.linuxx86_64/egg
running install_lib
warning: install_lib: 'build/lib' does not exist no Python modules
to install
creating build
```

**328** Глава 9. Управление пакетами

```
creating build/bdist.linuxx86_64
creating build/bdist.linuxx86_64/egg
creating build/bdist.linuxx86_64/egg/EGGINFO
copying symlinkator.egginfo/PKGINFO> build/bdist.linuxx86_64/egg/EGG
INFO
copying symlinkator.egginfo/SOURCES.txt> build/bdist.linuxx86_64/egg/
EGGINFO
copying symlinkator.egginfo/dependency_links.txt> build/bdist.linux
x86_64/egg/EGGINFO
copying symlinkator.egginfo/top_level.txt> build/bdist.linuxx86_64/egg/
EGGINFO
zip_safe flag not set; analyzing archive contents...
creating dist
creating 'dist/symlinkator0.1py2.5.egg' and adding 'build/bdist.linux
x86_64/egg' to it
removing 'build/bdist.linuxx86_64/egg' (and everything under it)
```
```
Проверим содержимое пакета. Для этого перейдем в предварительно
созданный каталог dist и проверим наличие пакета в этом каталоге:
```
```
$ lsl dist
total 4
rwrr 1 jmjones jmjones 825 20080503 15:34 symlinkator0.1py2.5.egg
```
```
Теперь установим пакет:
```
```
$ easy_install dist/symlinkator0.1py2.5.egg
Processing symlinkator0.1py2.5.egg
....
Processing dependencies for symlinkator==0.1
Finished processing dependencies for symlinkator==0.1
```
```
Затем запустим оболочку IPython, импортируем модуль и попробуем
им воспользоваться:
In [1]: from symlinkator.symlinkator import get_links
```
```
In [2]: get_links('/home/jmjones/logs/')
Out[2]: [('fetchmail.log.old', '/home/jmjones/logs/fetchmail.log.3', False),
('fetchmail.log', '/home/jmjones/logs/fetchmail.log.0', True)]
```
```
На всякий случай проверим содержимое каталога, который был пере
дан функции get_links():
$ lsl ~/logs/
total 0
lrwxrwxrwx 1 jmjones jmjones 15 20080503 15:11 fetchmail.log>
fetchmail.log.0
rwrr 1 jmjones jmjones 0 20080503 15:09 fetchmail.log.0
rwrr 1 jmjones jmjones 0 20080503 15:09 fetchmail.log.1
lrwxrwxrwx 1 jmjones jmjones 15 20080503 15:11 fetchmail.log.old>
fetchmail.log.3
```

Точки входа и сценарии консоли **329**

**Точки входа и сценарии консоли**

```
Со страницы документации проекта setuptools:
```
```
Точки входа используются для поддержки динамического обнару
жения служб или расширений, предоставляемых проектом. За до
полнительной информацией и примерами представления аргумен
тов обращайтесь к разделу «Dynamic Discovery of Services and Plu
gins». Кроме того, это ключевое слово (entry_points) используется
для поддержки автоматического создания сценариев.
```
```
В этой книге мы рассмотрим единственную разновидность точек вхо
да – различные сценарии консоли. setuptools автоматически создает
сценарий консоли, исходя из двух частей информации, которую вы
поместите в свой сценарий setup.py. Ниже приводится интересующий
нас раздел в файле setup.py из предыдущего примера:
entry_points = {
'console_scripts': [
'linkator = symlinkator.symlinkator:main',
],
},
```
```
В этом примере мы указали, что хотели бы получить сценарий linkator
и что при исполнении сценария он должен вызывать функцию main()
из модуля symlinkator.symlinkator. Во время установки пакета сцена
рий linkator был помещен в тот же каталог, где находится исполняе
мый файл python:
#!/home/jmjones/local/python/scratch/bin/python
# EASYINSTALLENTRYSCRIPT: 'symlinkator==0.1','console_scripts','linkator'
__requires__ = 'symlinkator==0.1'
import sys
from pkg_resources import load_entry_point
```
```
sys.exit(
load_entry_point('symlinkator==0.1', 'console_scripts', 'linkator')()
)
```
```
Все, что вы видите, было создано инструментом setuptools. Совершенно
необязательно понимать все, что находится в этом сценарии. В действи
тельности, вообще необязательно понимать хоть чтонибудь в этом
сценарии. Важно лишь знать, что, когда вы определяете в файле set
up.py точку входа console_scripts, setuptools создаст сценарий, который
будет вызывать ваш программный код, расположенный там, где вы
укажете. Ниже показано, что произошло, когда мы вызвали этот сце
нарий примерно так, как вызывали функцию в предыдущем примере:
$ linkator ~/logs/
[('fetchmail.log.old', '/home/jmjones/logs/fetchmail.log.3', False),
('fetchmail.log', '/home/jmjones/logs/fetchmail.log.0', True)]
```

**330** Глава 9. Управление пакетами

```
С точками входа связано несколько достаточно сложных аспектов, но
на верхнем уровне достаточно знать, что точки входа используются
для установки ваших сценариев, играющих роль инструментов ко
мандной строки, в каталог, доступный для пользователя. Для этого
вам необходимо следовать синтаксису, описанному выше, и опреде
лить функцию, которая должна вызываться вашим инструментом ко
мандной строки.
```
**Регистрация пакета в Python Package Index**

```
Если вы напишете действительно полезный модуль, вполне естествен
но, что вы захотите поделиться им с другими людьми. Это одна из са
мых приятных сторон в разработке открытого программного обеспече
ния. К счастью, выгрузить пакет в Python Package Index (каталог па
кетов Python) совсем несложно.
Этот процесс лишь немного отличается от процесса создания пакета.
При этом следует обратить внимание на две вещи: не забыть включить
описание в формате ReST (reStructuredText) в атрибут long_description
и подставить значение download_url. О формате ReST мы уже упомина
ли в главе 4.
Несмотря на то, что формат ReST уже обсуждался ранее, мы должны
здесь подчеркнуть, что применение формата ReST для оформления до
кументации необходимо потому, что она будет преобразована в формат
HTML после выгрузки пакета в Python Package Index. Вы можете вос
пользоваться инструментом ReSTless, созданным Аароном Хиллегас
сом (Aaron Hillegass), для предварительного просмотра форматирован
ного текста, чтобы убедиться, что он отформатирован должным обра
зом. Обязательно просматривайте документацию, чтобы убедиться в от
сутствии нарушений форматирования. Если текст не будет должным
образом отформатирован в формате ReST, после выгрузки модуля
текст будет отображаться как обычный текст, а не как HTML.
В примере 9.2 приводится содержимое файла setup.py для инструмен
та командной строки и библиотеки, созданной Ноа (Noah).
```
```
Пример 9.2. Пример файла setup.py для выгрузки модуля
в Python Package Index
#!/usr/bin/env python
```
```
# liten 0.1.4.2 deduplication commandline tool
#
# Author: Noah Gift
try:
from setuptools import setup, find_packages
except ImportError:
from ez_setup import use_setuptools
use_setuptools()
from setuptools import setup, find_packages
```

Регистрация пакета в Python Package Index **331**

```
import os,sys
version = '0.1.4.2'
f = open(os.path.join(os.path.dirname(__file__), 'docs', 'index.txt'))
long_description = f.read().strip()
f.close()
setup(
name='liten',
version='0.1.4.2',
description='a deduplication command line tool',
long_description=long_description,
classifiers=[
'Development Status :: 4 Beta',
'Intended Audience :: Developers',
'License :: OSI Approved :: MIT License',
],
author='Noah Gift',
author_email='noah.gift@gmail.com',
url='http://pypi.python.org/pypi/liten',
download_url="http://code.google.com/p/liten/downloads/list",
license='MIT',
py_modules=['virtualenv'],
zip_safe=False,
py_modules=['liten'],
entry_points="""
[console_scripts]
liten = liten:main
""",
)
```
```
C помощью этого файла setup.py теперь можно «автоматически» заре
гистрировать пакет в Python Package Index, используя следующую ко
манду:
$ python setup.py register
running register
running egg_info
writing liten.egginfo/PKGINFO
writing toplevel names to liten.egginfo/top_level.txt
writing dependency_links to liten.egginfo/dependency_links.txt
writing entry points to liten.egginfo/entry_points.txt
writing manifest file 'liten.egginfo/SOURCES.txt'
Using PyPI login from /Users/ngift/.pypirc
Server response (200): OK
```
```
В этом файле setup.py появились новые дополнительные поля, если
сравнивать его с предыдущим примером symlinkator. В число дополни
тельных полей входят description, long_description, classifiers, author
и download_url. Точка входа, обсуждавшаяся выше, позволяет запус
кать инструмент из командной строки и устанавливать его в каталог,
по умолчанию предназначенный для сценариев.
```

**332** Глава 9. Управление пакетами

```
Атрибут download_url имеет особо важное значение, потому что он сооб
щает сценарию easy_install, где искать ваш пакет. Сюда можно вклю
чить ссылку на страницу, и тогда сценарий easy_install самостоятель
но отыщет дистрибутив с исходными текстами или пакет в формате
.egg , но можно также указать прямую ссылку на пакет.
В атрибут long_description записывается существующее описание, ко
торое хранится в созданном нами файле index.txt в подкаталоге docs.
Файл index.txt содержит текст в формате ReST, а сценарий setup.py
в процессе регистрации пакета в Python Package Index читает эту ин
формацию и помещает ее в атрибут long_description.
```
**Где можно получить дополнительную информацию...**

```
Ниже приводится несколько важных ресурсов:
Easy install
http://peak.telecommunity.com/DevCenter/EasyInstall
Пакеты Python в формате .egg
http://peak.telecommunity.com/DevCenter/PythonEggs
Модуль setuptools
http://peak.telecommunity.com/DevCenter/setuptools
Модуль pkg_resources
http://peak.telecommunity.com/DevCenter/PkgResources
Обзор архитектуры модуля pkg_resources и формата Python egg в об>
щих чертах
Обзор архитектуры модуля pkg_resources и формата Python egg
вобщих чертах
И не забудьте про почтовую рассылку по языку Python http://mail.py>
thon.org/pipermail/distutilssig/.
```
**Distutils**

```
К моменту написания этих строк инструмент setuptools считался пред
почтительным способом создания и распространения пакетов и похо
же, что части библиотеки setuptools войдут в стандартную библиотеку
языка Python. Но при этом попрежнему важно знать, как работает па
кет distutils, возможности которого расширяет библиотека setuptools,
и какие возможности в нем отсутствуют.
Когда пакет создается с помощью distutils, обычно установка такого
пакета выполняется командой:
python setup.py install
```
```
Что касается вопроса сборки пакетов, готовых для распространения,
мы рассмотрим четыре следующие темы:
```

Distutils **333**

**-** Как написать сценарий установки, то есть файл _setup.py_
**-** Основные параметры настройки в файле _setup.py_
**-** Как собрать дистрибутив с исходными текстами
**-** Создание двоичных пакетов, например, в формате rpm для Red Hat,
    pkgtool для Solaris и swinstall для HPUX
Лучший способ продемонстрировать, как работает пакет distutils, –
это встать на ноги и приготовиться.
Шаг 1: создать некоторый программный код. Воспользуемся следую
щим сценарием, на примере которого продемонстрируем, как органи
зовать его распространение:

```
#!/usr/bin/env python
#A simple python script we will package
#Distutils Example. Version 0.1
class DistutilsClass(object):
"""Этот класс выводит информацию о самом себе."""
def __init__(self):
print "Hello, I am a distutils distributed script." \
"All I do is print this message."
if __name__ == '__main__':
DistutilsClass()
```
```
Шаг 2: создать файл setup.py в каталоге со сценарием.
#Installer for distutils example script
from distutils.core import setup
setup(name="distutils_example",
version="0.1",
description="A Completely Useless Script That Prints",
author="Joe Blow",
author_email = "joe.blow@pyatl.org",
url = "http://www.pyatl.org")
```
```
Обратите внимание: мы передали функции setup() несколько имено
ванных аргументов, значения которых позднее будут использоваться
как метаданные для идентификации пакета. Учтите, что это очень
простой пример и на самом деле эта функция имеет гораздо большее
число аргументов, которые, например, позволяют разрешать пробле
мы с зависимостями и другие. Мы не будем углубляться в исследова
ние дополнительных параметров настройки, но рекомендуем ознако
миться с ними в официальной электронной документации Python.
Шаг 3: создать дистрибутив.
Теперь, когда у нас имеется простенький сценарий setup.py, можно
создать пакет дистрибутива с исходными текстами, просто запустив
следующую команду в том же каталоге, где находится ваш сценарий
ифайлы README и setup.py :
```

**334** Глава 9. Управление пакетами

```
python setup.py sdist
```
```
Вы должны получить следующий вывод:
```
```
running sdist
warning: sdist: manifest template 'MANIFEST.in' does not exist
(using default file list)
writing manifest file 'MANIFEST'
creating distutils_example0.1
making hard links in distutils_example0.1...
hard linking README.txt distutils_example0.1
hard linking setup.py distutils_example0.1
creating dist
tarcf dist/distutils_example0.1.tar distutils_example0.1
gzipf9 dist/distutils_example0.1.tar
removing 'distutils_example0.1' (and everything under it)
```
```
Теперь все, что необходимо сделать для установки такого пакета, – это
распаковать его и выполнить команду:
```
```
python setup.py install
```
```
Ниже приводятся несколько примеров, которые пригодятся, если воз
никнет необходимость создать пакет в двоичном формате. Обратите
внимание, что эти способы тесно связаны с типом операционной систе
мы, поэтому вы не сможете собрать пакет в формате rpm, например,
в операционной системе OS X. Однако, учитывая изобилие продуктов
виртуализации, это не должно быть для вас большой проблемой. Дос
таточно хранить под рукой несколько виртуальных машин, которыми
можно было бы воспользоваться для сборки пакетов.
Чтобы собрать пакет rpm:
```
```
python setup.py bdist_rpm
```
```
Чтобы собрать пакет в формате pkgtool для Solaris:
```
```
python setup.py bdist_pkgtool
```
```
Чтобы собрать пакет в формате swinstall для HPUX:
```
```
python setup.py bdist_sdux
```
```
Наконец, когда вы выполняете компиляцию полученного пакета, вы
можете настроить каталог, где должна происходить сборка. Обычно
процессы компиляции и установки выполняются одновременно, но вы
можете выбрать для сборки свой каталог, как показано ниже:
python setup.py buildbuildbase=/mnt/python_src/ascript.py
```
```
Когда вы запустите команду install, она скопирует все, что находится
в каталоге сборки, в каталог установки. По умолчанию каталог уста
новки соответствует каталогу site>packages в каталоге установки ин
терпретатора Python, под управлением которого выполняется коман
```

Buildout **335**

```
да, но вы можете указать свой каталог установки , например, смонти
рованный каталог NFS, как в примере выше.
```
**Buildout**

```
Инструмент Buildout был создан Джимом Фултоном (Jim Fulton) из
корпорации Zope Corporation и предназначен для «сборки» новых при
ложений. Этими приложениями могут быть программы на языке Py
thon или другие программы, такие как Apache. Одна из основных це
лей Buildout состоит в том, чтобы обеспечить возможность установки
приложений на разных платформах. Одним из первых экспериментов,
которые провел автор с помощью Buildout, был эксперимент по раз
вертыванию сайта Plone 3.x. С тех пор он понял, что это была всего
лишь вершина айсберга.
Buildout – это один из наиболее интересных новых инструментов
управления пакетами, которые может предложить Python, так как он
позволяет сложным приложениям со сложными зависимостями раз
вертывать самих себя, если в дистрибутиве имеются файл bootstrap.py
и файл с настройками. В следующих разделах мы поделим наше обсу
ждение на две части: использование Buildout и разработка с примене
нием Buildout. Мы также рекомендовали бы вам прочитать руково
дство по Buildout по адресу: http://pypi.python.org/pypi/zc.buildout ,
так как этот неоценимый ресурс содержит самую свежую информацию
о Buildout. Эта документация настолько полная, насколько это воз
можно, и ее обязательно должен прочитать каждый пользователь
Buildout.
```
**Использование Buildout**

```
Несмотря на то, что многие, кто имел дело с технологиями Zope, знали
о существовании Buildout, это оставалось тайной для остальной части
пользователей Python. Buildout – это рекомендуемый механизм раз
вертывания Plone. Для тех, кто не знаком с Plone: это система управ
```
```
ПОРТ РЕТ ЗНА МЕ НИ ТО СТИ: BUILDOUT
```
**Джим Фултон (Jim Fulton)**

```
Джим Фултон – создатель и один из членов проекта Zope Object
Database. Джим также является одним из создателей Zope Object
Publishing Environment и техническим директором Zope Corpo
ration.
```

**336** Глава 9. Управление пакетами

```
ления содержимым для сайтов уровня предприятия, за которой стоит
огромное сообщество разработчиков. Система Plone была чрезвычайно
сложна в установке, пока не появился инструмент Buildout. Теперь
благодаря Buildout установка Plone выполняется тривиально просто.
Многие даже не подозревают, что Buildout можно использовать даже
для управления окружением Python. Buildout – это весьма интеллекту
альное программное обеспечение, которому требуется всего две вещи:
```
**-** Последняя версия _bootstrap.py_. Загрузить ее всегда можно по адресу
    _[http://svn.zope.org/*checkout*/zc.buildout/trunk/bootstrap/bootst>](http://svn.zope.org/*checkout*/zc.buildout/trunk/bootstrap/bootst>)_
    _rap.py_.
**-** Файл _buildout.cfg_ с именами пакетов для установки.
Лучший способ продемонстрировать возможности Buildout состоит
в том, чтобы установить чтонибудь с его помощью. Ноа (Noah) напи
сал инструмент командной строки для удаления дубликатов файлов,
который можно найти в центральном репозитарии Python, PyPI. Мы
попробуем с помощью Buildout развернуть среду Python для запуска
этого инструмента.
Шаг 1: загруить сценарий buildout.py:

```
mkdirp ~/src/buildout_demo
curl http://svn.zope.org/*checkout*/zc.buildout/trunk/
bootstrap/bootstrap.py > ~/src/buildout_demo/bootstrap.py
```
```
Шаг 2: написать простой файл buildout.cfg. Как уже говорилось выше,
Buildout требует для своей работы файл buildout.cfg. Если попытаться
запустить сценарий buildout.py без файла buildout.cfg , будет получено
следующее сообщение:
$ python bootstrap.py
While:
Initializing.
Error: Couldn't open /Users/ngift/src/buildout_demo/buildout.cfg
```
```
Для примера создадим конфигурационный файл, как показано в при
мере 9.3.
```
```
Пример 9.3. Пример конфигурационного файла Buildout
[buildout]
parts = mypython
[mypython]
recipe = zc.recipe.egg
interpreter = mypython
eggs = liten
```
```
Если сохранить этот файл с именем buildout.cfg и затем снова запус
тить сценарий buildout.py, будет получен вывод, как показано в при
мере 9.4.
```

Использование Buildout **337**

```
Пример 9.4. Создание окружения buildout
$ python bootstrap.py
Creating directory '/Users/ngift/src/buildout_demo/bin'.
Creating directory '/Users/ngift/src/buildout_demo/parts'.
Creating directory '/Users/ngift/src/buildout_demo/eggs'.
Creating directory '/Users/ngift/src/buildout_demo/developeggs'.
Generated script '/Users/ngift/src/buildout_demo/bin/buildout'.
```
```
Если заглянуть в эти вновь созданные каталоги, мы найдем выполняе
мые программы, включая отдельный интерпретатор Python в каталоге
bin :
```
```
$ lsl bin
total 24
rwxrxrx 1 ngift staff 362 Mar 4 22:17 buildout
rwxrxrx 1 ngift staff 651 Mar 4 22:23 mypython
```
```
Теперь, когда была выполнена установка инструмента Buildout, мож
но запустить его и пакет, который мы определили ранее, будет рабо
тать, как показано в примере 9.5.
```
```
Пример 9.5. Запуск Buildout и тестирование установки
$ bin/buildout
Getting distribution for 'zc.recipe.egg'.
Got zc.recipe.egg 1.0.0.
Installing mypython.
Getting distribution for 'liten'.
Got liten 0.1.3.
Generated script '/Users/ngift/src/buildout_demo/bin/liten'.
Generated interpreter '/Users/ngift/src/buildout_demo/bin/mypython'.
$ bin/mypython
>>>
$ lsl bin
total 24
rwxrxrx 1 ngift staff 362 Mar 4 22:17 buildout
rwxrxrx 1 ngift staff 258 Mar 4 22:23 liten
rwxrxrx 1 ngift staff 651 Mar 4 22:23 mypython
$ bin/mypython
```
```
>>> import liten
```
```
Наконец, т. к. сценарий «liten» был создан с использованием точки
входа, которые обсуждались ранее в этой главе, то при установке паке
та формата egg помимо модуля автоматически был установлен кон
сольный сценарий в локальный каталог bin в окружении Buildout. Ес
ли попробовать запустить этот сценарий, будет получен вывод, как по
казано ниже:
$ bin/liten
Usage: liten [starting directory] [options]
```

**338** Глава 9. Управление пакетами

```
A commandline tool for detecting duplicates using md5 checksums.
Options:
version show program's version number and exit
h,help show this help message and exit
c,config Path to read in config file
s SIZE,size=SIZE File Size Example: 10bytes, 10KB, 10MB,10GB,10TB, or
plain number defaults to MB (1 = 1MB)
q,quiet Suppresses all STDOUT.
r REPORT,report=REPORT
Path to store duplication report. Default CWD
t,test Runs doctest.
$ pwd
/Users/ngift/src/buildout_demo
```
```
Это очень простой и яркий пример, демонстрирующий, как можно ис
пользовать Buildout для создания изолированной среды и автоматиче
ски развертывать все необходимые зависимости проекта и самой сре
ды. Тем не менее, чтобы продемонстрировать истинную мощь Build
out, нам необходимо рассмотреть еще один аспект этого инструмента.
Buildout обладает полным «контролем» над каталогом, в котором он
выполняется, и при каждом запуске он читает файл buildout.cfg в по
исках инструкций. Это означает, что если удалить пакет egg из спи
ска, это приведет к удалению инструмента командной строки и биб
лиотеки, как показано в примере 9.6.
```
```
Пример 9.6. Удаление записей из конфигурационного файла Buildout
[buildout]
parts =
```
```
Ниже приводится результат повторного запуска Buildout после удале
ния пакетов и интерпретатора из списка. Обратите внимание, что
у Buildout имеется множество параметров командной строки и в дан
ном случае мы указали ключ –N, который всего лишь модифицирует
измененные файлы. Обычно, на каждом перезапуске Buildout пере
страивает полностью свою среду.
```
```
$ bin/buildoutN
Uninstalling mypython.
```
```
Если теперь заглянуть в каталог bin , можно будет убедиться, что ин
терпретатор и инструмент командной строки исчезли. Единственное,
что осталось в нем, это сам инструмент командной строки Buildout:
$ lsl bin/
total 8
rwxrxrx 1 ngift staff 362 Mar 4 22:17 buildout
```
```
Однако, если заглянуть в каталог eggs , можно увидеть, что пакет уста
новлен, но неактивен. Мы не сможем запустить его, так как интерпре
татор отсутствует:
```

Разработка с использованием Buildout **339**

```
$ lsl eggs
total 640
drwxrxrx 7 ngift staff 238 Mar 4 22:54 liten0.1.3py2.5.egg
rwrr 1 ngift staff 324858 Feb 16 23:47 setuptools0.6c8py2.5.egg
drwxrxrx 5 ngift staff 170 Mar 4 22:17 zc.buildout1.0.0py2.5.egg
drwxrxrx 4 ngift staff 136 Mar 4 22:23 zc.recipe.egg1.0.0py2.5.egg
```
**Разработка с использованием Buildout**

```
Теперь, когда мы рассмотрели простой пример создания и удаления
среды, управляемой инструментом Buildout, мы можем двинуться
дальше и создать среду разработки, управляемую Buildout.
Один из наиболее типичных сценариев, когда используется Buildout,
выглядит очень просто. Разработчик может работать над отдельным
пакетом, который находится в репозитарии системы управления вер
сиями. Разработчик извлекает свой проект в каталог верхнего уровня
src. Внутри этого каталога он запускает Buildout, как было описано
выше, примерно с таким конфигурационным файлом:
```
```
[buildout]
develop =.
parts = test
[python]
recipe = zc.recipe.egg
interpreter = python
eggs = ${config:mypkgs}
[scripts]
recipe = zc.recipe.egg:scripts
eggs = ${config:mypkgs}
```
```
[test]
recipe = zc.recipe.testrunner
eggs = ${config:mypkgs}
```
**virtualenv**

```
Согласно описанию на странице в Python Package Index: «virtualenv –
это инструмент создания изолированной среды Python». Основная за
дача, которую решает virtualenv, состоит в устранении конфликтов
между пакетами. Часто один инструмент требует одну версию некото
рого пакета, а другой инструмент – другую версию того же пакета. Это
может породить ситуацию, когда будет нарушена работоспособность
рабочего вебприложения, потому что ктото «случайно» изменит со
держимое глобального каталога site>packages , обновив пакет, чтобы
получить возможность пользоваться другим инструментом.
С другой стороны, разработчик может не иметь права на запись в гло
бальный каталог site>packages , и тогда он может с помощью virtualenv
```

**340** Глава 9. Управление пакетами

```
создать виртуальную среду, изолированную от системной среды Py
thon. Инструмент virtualenv предоставляет отличный способ ликвиди
ровать проблемы еще до того, как они появятся, так как он позволяет
создать новую среду, частично или полностью изолированную от гло
бального каталога site>packages.
Инструмент virtualenv также может «развертывать» виртуальную
среду, позволяя разработчикам наполнять ее только требуемыми па
кетами. По своему действию он очень напоминает Buildout, но в отли
чие от последнего не использует декларативный конфигурационный
файл. Следует заметить, что оба инструмента, Buildout и virtualenv,
очень широко используют библиотеку setuptools, сопровождением ко
торой в настоящее время занимается Филлип Дж. Эби (Phillip J. Eby).
```
```
Так как же пользоваться инструментом virtualenv? Самый простой
способ – установить его с помощью easy_install:
```
```
sudo easy_install virtualenv
```
```
Если вы планируете использовать virtualenv только с одной версией
Python, такой подход вполне оправдывает себя. Если у вас установле
но несколько версий Python, например, Python 2.4, Python 2.5, Python
2.6 и, возможно, Python 3000, и при этом они установлены в один и тот
же каталог bin , такой как /usr/bin , тогда лучше будет использовать
иной подход, поскольку в один и тот же каталог можно установить
только один сценарий virtualenv.
Один из способов получить несколько сценариев virtualenv, работаю
щих с разными версиями Python, состоит в том, чтобы просто загру
зить последнюю версию virtualenv и создать псевдонимы для каждой
версии Python. Ниже описывается, как это сделать:
```
1. curl _[http://svn.colorstudy.com/virtualenv/trunk/virtualenv.py](http://svn.colorstudy.com/virtualenv/trunk/virtualenv.py) > virtu>_
    _alenv.py_
2. sudo cp _virtualenv.py /usr/local/bin/virtualenv.py_

```
ПОРТ РЕТ ЗНА МЕ НИ ТО СТИ: VIRTUALENV
```
**Ян Бикинг (Ian Bicking)**

```
Ян Бикинг отвечает за такое количество пакетов
Python, что ему часто бывает сложно уследить за
всем. Им был написан пакет Webob, являющийся
частью Google App Engine, Paste, virtualenv, SQL
Object и многие другие пакеты. Вы можете посе
тить его блог по адресу: http://blog.ianbicking.org/.
```

virtualenv **341**

3. Создать два псевдонима в командной оболочке Bash или zsh:
    alias virtualenvpy24="/usr/bin/python2.4 /usr/local/bin/virtualenv.py"
    alias virtualenvpy25="/usr/bin/python2.5 /usr/local/bin/virtualenv.py"
    alias virtualenvpy26="/usr/bin/python2.6 /usr/local/bin/virtualenv.py"

```
Создав среду с несколькими сценариями, можно двинуться дальше
и создать несколько контейнеров virtualenv, по одному для каждой
версии Python, которые нам потребуются. Ниже показано, как это де
лается.
Создание виртуальной среды Python 2.4:
$ virtualenvpy24 /tmp/sandbox/py24ENV
New python executable in /tmp/sandbox/py24ENV/bin/python
Installing setuptools.................done.
$ /tmp/sandbox/py24ENV/bin/python
Python 2.4.4 (#1, Dec 24 2007, 15:02:49)
[GCC 4.0.1 (Apple Inc. build 5465)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>
$ ls /tmp/sandbox/py24ENV/
bin/ lib/
$ ls /tmp/sandbox/py24ENV/bin/
activate easy_install* easy_install2.4* python* python2.4@
```
```
Создание виртуальной среды Python 2.5:
```
```
$ virtualenvpy25 /tmp/sandbox/py25ENV
New python executable in /tmp/sandbox/py25ENV/bin/python
Installing setuptools..........................done.
$ /tmp/sandbox/py25ENV/bin/python
Python 2.5.1 (r251:54863, Jan 17 2008, 19:35:17)
[GCC 4.0.1 (Apple Inc. build 5465)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>
$ ls /tmp/sandbox/py25ENV/
bin/ lib/
$ ls /tmp/sandbox/py25ENV/bin/
activate easy_install* easy_install2.5* python* python2.5@
```
```
Если внимательно взглянуть на вывод команд, можно заметить, что
virtualenv создал каталоги bin и lib. Внутри каталога bin находится ин
терпретатор python, который использует каталог lib как собственный
локальный каталог site>packages. Другой заметной особенностью яв
ляется наличие предустановленного сценария easy_install, что позво
ляет устанавливать пакеты в виртуальную среду.
Наконец, важно отметить, что существует два способа работы с создан
ной виртуальной средой. Можно запустить виртуальную среду, явно
указав полный путь к интерпретатору:
$ /src/virtualenvpy24/bin/python2.4
```

**342** Глава 9. Управление пакетами

```
Или можно использовать сценарий activate, находящийся в каталоге
bin требуемой виртуальной среды, чтобы активировать эту среду без
ввода полного пути. Это дополнительный способ, который вы можете
использовать, но он не является обязательным, так как вы всегда мо
жете ввести полный путь к требуемой виртуальной среде. Дуг Хелл
манн (Doug Hellmann), один из рецензентов этой книги, написал инте
ресный сценарий, доступный по адресу: http://www.doughellmann.com
/projects/virtualenvwrapper/. Он использует сценарий activate и меню
на языке Bash, которое позволяет выбирать, какая среда должна быть
запущена.
```
**Создание собственных виртуальных окружений**

```
Версия virtualenv 1.0, которая была текущей на момент написания
этой книги, включает поддержку создания сценариев развертывания
собственных виртуальных окружений. Добиться этого можно с помо
щью функции virtualenv.create_bootstrap_script(text). Эта функция
создает сценарий развертывания, который по своему действию напо
минает virtualenv, но обладает расширенными возможностями анали
за параметров с помощью функций, определяемых пользователем,
extend_parser() и adjust_options(), и позволяет выполнять действия по
сле установки с помощью функции after_install().
Давайте посмотрим, насколько просто создать собственный сценарий
развертывания, который установит virualenv и заданный набор паке
тов в новую среду. Возьмем опять в качестве примера пакет liten. Мы
можем с помощью virtualenv создать совершенно новую виртуальную
среду и установить в нее пакет liten. В примере 9.7 показано, как соз
дается сценария развертывания собственной виртуальной среды, кото
рый устанавливает пакет liten.
```
```
Пример 9.7. Пример сценария, развертывающего новую виртуальную среду
import virtualenv, textwrap
output = virtualenv.create_bootstrap_script(textwrap.dedent("""
import os, subprocess
def after_install(options, home_dir):
etc = join(home_dir, 'etc')
if not os.path.exists(etc):
os.makedirs(etc)
subprocess.call([join(home_dir, 'bin', 'easy_install'),
'liten'])
"""))
f = open('litenbootstrap.py', 'w').write(output)
```
```
Этот сценарий является измененной версией примера из документа
ции к virtualenv и здесь особое внимание следует обратить на послед
ние две строки:
```
```
subprocess.call([join(home_dir, 'bin', 'easy_install'),
'liten'])
```

virtualenv **343**

```
"""))
f = open('litenbootstrap.py', 'w').write(output)
```
```
В двух словах, эти строки предписывают функции after_install() вы
полнить запись в новый файл с именем liten>bootstrap.py , расположен
ный в текущем рабочем каталоге, и затем с помощью easy_install уста
новить модуль liten. Важно отметить, что этот фрагмент программного
кода создаст файл bootstrap.py , который затем будет использоваться
при запуске. После запуска этого сценария мы получим файл liten>
bootstrap.py , который потом можно передать разработчику или конеч
ному пользователю.
Если запустить сценарий liten–bootstrap.py без параметров, от него бу
дет получен следующий вывод:
```
```
$ python litenbootstrap.py
You must provide a DEST_DIR
Usage: litenbootstrap.py [OPTIONS] DEST_DIR
Options:
version show program's version number and exit
h,help show this help message and exit
v,verbose Increase verbosity
q,quiet Decrease verbosity
clear Clear out the nonroot install and start from scratch
nositepackages Don't give access to the global sitepackages dir to the
virtual environment
```
```
Если запустить этот сценарий, указав ему каталог назначения, будет
получен следующий вывод:
$ python litenbootstrap.pynositepackages /tmp/litenENV
New python executable in /tmp/litenENV/bin/python
Installing setuptools..........................done.
Searching for liten
Best match: liten 0.1.3
Processing liten0.1.3py2.5.egg
Adding liten 0.1.3 to easyinstall.pth file
Installing liten script to /tmp/litenENV/bin
Using /Library/Python/2.5/sitepackages/liten0.1.3py2.5.egg
Processing dependencies for liten
Finished processing dependencies for liten
```
```
Наш интеллектуальный сценарий автоматически создал среду с на
шим модулем. Если теперь запустить инструмент liten с полным путем
к виртуальной среде, мы получим следующее:
$ /tmp/litenENV/bin/liten
Usage: liten [starting directory] [options]
A commandline tool for detecting duplicates using md5 checksums.
```
```
Options:
```

**344** Глава 9. Управление пакетами

```
version show program's version number and exit
h,help show this help message and exit
c,config Path to read in config file
s SIZE,size=SIZE File Size Example: 10bytes, 10KB, 10MB,10GB,10TB, or
plain number defaults to MB (1 = 1MB)
q,quiet Suppresses all STDOUT.
r REPORT,report=REPORT
Path to store duplication report. Default CWD
t,test Runs doctest.
```
```
Этот прием стоит того, чтобы знать о нем, так как он позволяет созда
вать полностью изолированную и развернутую виртуальную среду.
Мы надеемся, что в этом разделе нам удалось показать одно из основ
ных преимуществ virutalenv – его простоту в использовании и изуче
нии. Больше, чем что бы то ни было, virtualenv почитает священное
правило KISS (keep its syntax simple – сохраняй синтаксис как можно
проще), и одной этой причины уже достаточно, чтобы подумать об ис
пользовании этого инструмента для управления изолированными сре
дами разработки. Если у вас имеются дополнительные вопросы, касаю
щиеся этого инструмента, обязательно посетите почтовую рассылку
virtualenv по адресу http://groups.google.com/group/python>virtualenv/.
```
**Менеджер пакетов EPM**

```
Менеджер пакетов EPM создает «родные» пакеты для каждой опера
ционной системы, поэтому он должен присутствовать в любой систе
ме, где производится «сборка» пакетов. Благодаря невероятным успе
хам технологий виртуализации за последние несколько лет не состав
ляет никакого труда установить и настроить несколько виртуальных
машин. Я создал маленький кластер виртуальных машин (с мини
мальным потреблением памяти), которые загружаются в режиме, эк
вивалентном уровню 3 в Red Hat, – чтобы проверить примеры про
граммного кода, которые приводятся в этой книге.
Впервые возможности EPM продемонстрировал мне мой коллега, ко
торый одновременно является одним из разработчиков EPM. Я тогда
искал инструмент, который позволил бы мне создавать пакеты про
граммного обеспечения, которое я разрабатывал, в зависимости от ти
па операционной системы, и он назвал EPM. После прочтения некото
рой документации на сайте http://www.epmhome.org/epm>book.html
я был приятно удивлен, насколько простым и безболезненным оказал
ся процесс создания пакетов. В этом разделе мы пройдем все этапы
создания пакета программного обеспечения, готового к установке на
самых разных платформах: Ubuntu, OS X, Red Hat, Solaris и FreeBSD.
Эти шаги легко могут быть применены к другим системам, поддержи
вающим EPM, таким как AIX или HPUX.
```

Менеджер пакетов EPM **345**

```
Прежде чем перейти к изучению, приведу некоторые начальные сведе
ния о EPM. Согласно официальной документации, этот менеджер па
кетов изначально предусматривал сборку дистрибутивов программно
го обеспечения в двоичном формате, на основе общей спецификации
формата. Благодаря такой постановке задачи одни и те же файлы ди
стрибутивов могли использоваться в любых операционных системах
идля всех форматов.
```
**Требования и установка менеджера пакетов EPM**

```
Для установки EPM требуется только командная оболочка типа
Bourne shell, компилятор языка C и программы make и gzip. Все эти
утилиты легко получить практически в любой UNIXподобной систе
ме, если они уже не установлены. После того как исходные тексты
EPM будут загружены, необходимо выполнить следующую последова
тельность команд:
```
```
./configure
make
make install
```
**Создание дистрибутива Hello World с инструментом**

**командной строки**

```
Прежде чем приступать к созданию пакетов для любой UNIXподобной
системы, необходимо иметь чтолибо, что требуется упаковать. В духе
сложившихся традиций мы сначала создадим простой инструмент ко
мандной строки с именем hello_epm.py, как показано в примере 9.8.
```
```
Пример 9.8. Инструмент командной строки Hello EPM
#!/usr/bin/env python
import optparse
```
```
def main():
p = optparse.OptionParser()
p.add_option('os', 'o', default="*NIX")
options, arguments = p.parse_args()
print 'Hello EPM, I like to make packages on %s' % options.os
if __name__ == '__main__':
main()
```
```
Если запустить этот сценарий, будет получен следующий вывод:
```
```
$ python hello_epm.py
Hello EPM, I like to make packages on *NIX
```
```
$ python hello_epm.pyos RedHat
Hello EPM, I like to make packages on RedHat
```

**346** Глава 9. Управление пакетами

**Создание платформозависимых пакетов**

**с помощью EPM**

```
«Основы» использования настолько просты, что может вызвать у вас
недоумение, почему раньше вы никогда не пользовались EPM для упа
ковки кроссплатформенного программного обеспечения. EPM читает
файл(ы) с «перечнем», описывающим ваш пакет с программным обес
печением. Комментарии в этом «перечне» начинаются с символа #, ди
рективы – с символа %, переменные – с символа $ и, наконец, строки
с именами файлов, каталогов, сценариев инициализации и символи
ческих ссылок начинаются с алфавитного символа.
С помощью EPM можно создавать как универсальные кроссплатфор
менные сценарии установки, так и платформозависимые пакеты. Мы
сосредоточимся на создании платформозависимых файлов пакетов.
Следующий шаг на пути к созданию платформозависимого пакета за
ключается в создании манифеста, или «перечня», описывающего па
кет. В примере 9.9 приводится шаблон манифеста, использовавшийся
нами для создания пакета с нашим инструментом командной строки
hello_epm. Вообще, этот шаблон является настолько универсальным,
что вы можете использовать его с незначительными изменениями для
создания своих собственных инструментов.
```
```
Пример 9.9. Шаблон «перечня» для EPM
#Файл перечня для EPM может использоваться для создания пакетов под
#любую из следующих платформ
#epmf format foo bar.list ENTER
#Параметр format может быть одним из следующих ключевых слов:
#aix пакеты с программным обеспечением для AIX.
#bsd пакеты с программным обеспечением для FreeBSD, NetBSD или OpenBSD.
#depot или swinstall пакеты с программным обеспечением для HPUX.
#dpkg пакеты с программным обеспечением для Debian.
#inst или tardist пакеты с программным обеспечением для IRIX.
#native "родные" для текущей платформы пакеты (RPM, INST, DEPOT, PKG, ...).
#osx пакеты с программным обеспечением для MacOS X.
#pkg пакеты с программным обеспечением для Solaris.
#portable переносимые пакеты с программным обеспечением (по умолчанию).
#rpm пакеты с программным обеспечением для Red Hat.
#setld пакеты с программным обеспечением для Tru64 (setld).
#slackware пакеты с программным обеспечением для Slackware.
# Раздел с информацией о продукте
```
```
%product hello_epm
%copyright 2008 Py4SA
%vendor O’Reilly
%license COPYING
%readme README
%description Command Line Hello World Tool
%version 0.1
```

Менеджер пакетов EPM **347**

```
# Переменные для сценария автоматической конфигурации
$prefix=/usr
$exec_prefix=/usr
$bindir=${exec_prefix}/bin
$datadir=/usr/share
$docdir=${datadir}/doc/
$libdir=/usr/lib
$mandir=/usr/share/man
$srcdir=.
# Выполняемые файлы
```
```
%system all
f 0555 root sys ${bindir}/hello_epm hello_epm.py
```
```
# Документация
%subpackage documentation
f 0444 root sys ${docdir}/README $srcdir/README
f 0444 root sys ${docdir}/COPYING $srcdir/COPYING
f 0444 root sys ${docdir}/hello_epm.html $srcdir/doc/hello_epm.html
# Файлы страниц справочного руководства (man)
```
```
%subpackage man
%description Man pages for hello_epm
f 0444 root sys ${mandir}/man1/hello_epm.1 $srcdir/doc/hello_epm.man
```
```
Если заглянуть внутрь файла, который мы назвали hello_epm.list ,
можно заметить, что мы определили переменную $srcdir, значение ко
торой соответствует текущему рабочему каталогу. Чтобы создать па
кет для любой из возможных платформ, нам необходимо создать в те
кущем рабочем каталоге следующие файлы и каталоги: README ,
COPYING , doc/hello_epm.html и doc/hello_epm.man. В этом же катало
ге должен находиться и наш сценарий hello_epm.py.
При желании мы можем «обмануть» EPM, просто поместив пустые
файлы в каталог, который предполагается упаковать, выполнив сле
дующие команды:
$ pwd
/tmp/release/hello_epm
$ touch README
$ touch COPYING
$ mkdir doc
$ touch doc/hello_epm.html
$ touch doc/hello_epm.man
```
```
Если теперь заглянуть в наш каталог, можно увидеть следующее его
содержимое:
```
```
$ lslR
total 16
rwrr 1 ngift wheel 0 Mar 10 04:45 COPYING
```

**348** Глава 9. Управление пакетами

```
rwrr 1 ngift wheel 0 Mar 10 04:45 README
drwxrxrx 4 ngift wheel 136 Mar 10 04:45 doc
rwrr 1 ngift wheel 1495 Mar 10 04:44 hello_epm.list
rwrr@ 1 ngift wheel 278 Mar 10 04:10 hello_epm.py
```
```
./doc:
total 0
rwrr 1 ngift wheel 0 Mar 10 04:45 hello_epm.html
rwrr 1 ngift wheel 0 Mar 10 04:45 hello_epm.man
```
**Создание пакета**

```
Теперь у нас имеется каталог с файлом «перечня», содержащий дирек
тивы, которые могут быть выполнены на любой платформе, где имеет
ся поддержка EPM. Теперь все, что осталось сделать, это запустить ко
манду epm – f, добавив к ней название платформы и имя файла перечня.
В примере 9.10 показано, как это выглядит в OS X.
```
```
Пример 9.10. Создание «родного» для OS X инсталлятора с помощью EPM
$ epmf osx hello_epm hello_epm.list
epm: Product names should only contain letters and numbers!
^C
$ epmf osx helloEPM hello_epm.list
$ ll
total 16
rwrr 1 ngift wheel 0 Mar 10 04:45 COPYING
rwrr 1 ngift wheel 0 Mar 10 04:45 README
drwxrxrx 4 ngift wheel 136 Mar 10 04:45 doc
rwrr 1 ngift wheel 1495 Mar 10 04:44 hello_epm.list
rwrr@ 1 ngift wheel 278 Mar 10 04:10 hello_epm.py
drwxrwxrwx 6 ngift staff 204 Mar 10 04:52 macosx10.5intel
```
```
Обратите внимание на предупреждение, которое было получено при
попытке использовать символ подчеркивания в имени пакета. По этой
причине мы дали пакету другое название и повторно запустили коман
ду. В результате был создан каталог macosx>10.5>intel со следующим
содержимым:
```
```
$ lsla macosx10.5intel
total 56
drwxrwxrwx 4 ngift staff 136 Mar 10 04:54.
drwxrxrx 8 ngift wheel 272 Mar 10 04:54 ..
rwrr@ 1 ngift staff 23329 Mar 10 04:54 helloEPM0.1macosx10.5
intel.dmg
drwxrxrx 3 ngift wheel 102 Mar 10 04:54 helloEPM.mpkg
```

Это очень удобно, так как был создан файл архива .dmg , который явля
ется «родным» форматом для OS X, содержащий наш инсталлятор
и инсталлятор, «родной» для OS X.
Если теперь запустить установку, можно заметить, что OS X установит
пустые файлы со страницей справочного руководства и с документацией и выведет содержимое пустого файла с лицензионным соглаше
нием. В конечном итоге наш инструмент будет помещен точно туда,
куда было указано, и ему будет присвоено заданное нами имя:
$ which hello_epm
/usr/bin/hello_epm
$ hello_epm
Hello EPM, I like to make packages on *NIX
$ hello_epmh
Usage: hello_epm [options]
Options:
h,help show this help message and exit
o OS,os=OS
$

**Вывод: EPM действительно прост в использовании**


Если с помощью команды scp – r скопировать каталог /tmp/release/hel
lo_epm в Red Hat, Ubuntu или Solaris, мы сможем выполнить одну и ту
же команду создания пакета, за исключением названия платформы,
и она «просто будет работать». В главе 8 мы узнали, как создать «фер
му» для сборки, чтобы вы могли моментально создавать кроссплат
форменные пакеты. Обратите внимание, что все представленные ис
ходные тексты примеров наряду с созданным пакетом, доступны для
загрузки. Теперь у вас есть все необходимые знания, чтобы за несколь
ко минут, немного изменив пример, начать создавать свои собствен
ные кроссплатформенные пакеты.
Менеджер пакетов EPM в состоянии предложить еще целый ряд допол
нительных особенностей, но они выходят далеко за рамки этой книги.
Если вам интересно узнать о том, как создавать пакеты с учетом зави
симостей, как запускать пред и постустановочные сценарии и так да
лее, то вам следует обратиться непосредственно к официальной доку
ментации EPM, где описываются все эти случаи и многое другое.
```
