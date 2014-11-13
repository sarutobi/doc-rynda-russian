=============
Быстрый старт
=============

0. Создаем и активируем virtualenv::

    $ virtualenv rynda
    $ source rynda/bin/activate

#. Клонируем репозиторий::

    $ git clone https://github.com/sarutobi/Rynda.git

#. Переходим в клонированный репозиторий::

    $ cd Rynda

#. Устанавливаем все зависимости::

    $ pip install -r requirements\test.txt

#. Копируем mysettings.py.example в mysettings.py

#. Задаем структуру базы данных и пароль суперпользователя::

    $ bash createdb.sh 

 Дважды вводим пароль суперпользователя. Имя суперпользователя по умолчанию: *admin*

6. Запускаем локальный сервер::

    $ python manage.py runserver

#. Открываем в браузере `http://localhost:8000 <http://localhost:8000>`_

#. Чтобы протестировать систему запускаем::

    $ python manage.py test

