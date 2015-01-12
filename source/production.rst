=================================
Установка Рынды в «боевом» режиме
=================================

Для установки в боевом режиме под веб сервером Apache потребуется установить модуль mod-wsgi. В ОС Debian это можно сделать командой::

    $ sudo aptitude install libapache2-mod-wsgi

.. note:: Стоит заметить, что если у вас стоит модуль ``mpm-itk``, то он может быть причиной того, что модуль ``mod-wsgi`` не будет работать.

Настройки VirtualHost
---------------------

Запуск в режиме демона является рекомендуемым методом запуска  ``mod_wsgi`` на не Windows  платформах. Чтобы создать процесс, в описание VirtualHost потребуется добавить директивы ``WSGIDaemonProcess``, ``WSGIProcessGroup`` и ``WSGIScriptAlias``.

* WSGIDaemonProcess - основная директива настройки демона, её формат::

    WSGIDaemonProcess имя_процесса processes= threads= python-path=

..

    где::

        python-path — это путь к библиотекам виртуального окружения сайта в формате:
        /path/to/mysite.com:/path/to/your/venv/lib/python2.X/site-packages

        processes — определяет количество процессов демона, которые должны быть запущены
        в этой группе процессов, определенной директивой WSGIProcessGroup. По умолчанию
        (когда кол-во не определено) будет запущен только один процесс.

        threads — определяет кол-во потоков для обработки запросов по каждому из процессов
        демона внутри группы процессов, определенных директивой WSGIProcessGroup.

* WSGIProcessGroup - группирует процессы демона::

    WSGIProcessGroup имя_процесса

* WSGIScriptAlias - местоположение скрипта, обрабатывающего запросы к приложению::

    WSGIScriptAlias site_root /path/to/mysite.com/wsgi.py

Пример::

    <Directory /path/to/mysite.com/mysite/Rynda/scripts>
    <Files wsgi.py>
            Require all granted
    </Files>
    </Directory>

.. note:: 
    если вы используете Apache версии 2.4 или старее, замените одну строку ``Require all granted`` на

    ::

        Order deny, allow
        Allow from all

Настройки обработки статических файлов
--------------------------------------
В отличие от сайтов, написанных на php, Django не обрабатывает статические файлы (изображения, таблицы стилей, скрипты). Их обработка должна настраиваться отдельно.

Для сервера Apache::

    Аlias /static/ /path/to/mysite.com/static

Для сервера nginx::

    location /static {alias /path/to/mysite.com/static; }


.. note:: рекомендуется использовать nginx в качестве backend'a для раздачи статических файлов, так как он делает это более эффективно, нежели Apache

Теперь запустим команду по сбору статических файлов, которая в автоматическом режиме разместит все статические файлы в правильном месте::

    ./manage.py collectstatic

Пример описания виртуального хоста для Apache
---------------------------------------------

.. code-block:: html

    <Directory /path/to/my/site/www/demo.openrynda.com/Rynda/rynda/Rynda>
        <Files wsgi.py>
            Order allow,deny
            Allow from all
        </Files>
    </Directory>

    <VirtualHost 1.2.3.4:81 >
            ServerName demo.openrynda.com
            DocumentRoot /path/to/my/site/www/demo.openrynda.com
            WSGIDaemonProcess openrynda processes=5 threads=1 python-path=/path/to/my/site/www/demo.openrynda.com/Rynda:/path/to/my/site/www/demo.openrynda.com/rynda/lib/python2.7/site-packages:/path/to/my/site/www/demo.openrynda.com/rynda/lib/python2.7/
            WSGIProcessGroup openrynda
            WSGIScriptAlias / /path/to/my/site/www/demo.openrynda.com/rynda/Rynda/wsgi.py

            Аlias /static/ /path/to/my/site/www/demo.openrynda.com/static  # расположение статики (при необходимости измените)
            ServerAlias www.demo.openrynda.com
    </VirtualHost>
