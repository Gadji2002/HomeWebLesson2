##ДЗ

### Подготовка
Форкаем как и на прошлом занятии.

открываем терминал _ctrl+alt+T_ созадаем новую папку для ДЗ в PyLessons:

_cd PyLessons_

_mkdir HomeWorks_

_cd HomeWorks_

_git clone "форкнутый репозиторий"_

открываем в pycharm, то что склонировали

теперь нужно создать виртуальное окружение. Создадим его через средства Pycharm

В Pycharm Ctrl+Alt+S затем Project -> Project interpreter -> Жмем на иконку шестеренки 
-> Add local -> В location задайте путь в папку venvs(Говорил об этом на занятии) 
-> OK

переходим в консоль Pycharma. Скорее всего ее нужно перегрузить
жмем на красный крестик и снова открываем консоль Pycharm

наше созданное виртуальное окружение должно быть подключено (слева в консоли будет в скобках название)

теперь набираем команду:

_pip install -r requirements.txt_

чтоб установить все необходимые библиотеки в venv


### 1. Реализуем обработку формы
Добавьте в index.py в начало функции def home()



    if request.method == 'POST':
        name = request.form.get('name', 'default_name')
        year = request.form.get('year', 1990)
        male = request.form.get('male', False)
        print('name=', name)
        print('year=', year)
        print('male=', male)

Так мы примем данные, которые прийдут из формы, и
выведем в консоль. 
Запустите index.py и заполнив форму отправте ее. 
Посмотрите в консоль там должны быть те данные которые мы вывели.

у некоторых была ошибка: 
_400 Bad request. The browser (or proxy) sent a request that this server could not understand._

Она может происходит если вы пытается взять параметр которые не передаете:
_name = request.form["name"]_ если в форме вдруг не окажется input c name=name то получите такую ошибку.
Чтоб избежать подобного надо использовать такую форму
_name = request.form.get('name', 'default_name')_ - получаем данные через метод get. Второй параметр
_default_name_ 

### 2. Подключаем БД 

добавим в index.py две функции:


    @app.before_request
    def before_request():
        db.connect()


    @app.after_request
    def after_request(response):
        db.close()
        return response
        
первая подключается к db (db которая объявлена в models.py) когда мы запускам index.py.
вторая отключается от db когда мы выключаем index.py

также заменим содержимое if request.method == 'POST':
    
    if request.method == 'POST':
        name = request.form.get('name', 'default_name')
        year = request.form.get('year', 1990)
        male = request.form.get('male', False)
        Person.create(name=name, year=year, male=male)
        
Вместо вывода в консоль, то, что в форме мы сохранием теперь в БД.

Теперь после if request.method == 'POST'

    persons = Person.select()
    return render_template('home.html', persons=persons)
    

Мы берем всех Person, которые у нас есть в БД.
И отправляем их в шаблон


теперь добавим в шаблон home.html после тега form этот код ,чтоб вывести persons их:

    {% for person in persons %}
        <div>
            <h3>{{ person.name }}</h3>
            <h6>{{ person.year }}</h6>
            <h6>{% if person.male %} Мужчина {% else %} Женщина {% endif %}</h6>
        </div>
    {% endfor %}
    
    
Все должно работать

**Закомитьте и запуште результат работы.**


### 3. Это все EASY а теперь настоящая ДЗ

необходимо home разделить на две сущности person и post.

Функция post должна содержать форму и сохранять результат в БД

А функция person должна выводит всех пользователей.

По сути мы просто разделяем функционал home на post и person

У каждой из функции(как наверно догадались) должны быть свои post.html и person.html

Ссылки на функции зайдайте какие угодно


**Закомитьте и запуште результат работы.**

### 4. Не забываем про base.html

Создайте также base.html и вынесите то, что одинаково для post.html и person.html

Должны быть ссылки на страницы post и person

