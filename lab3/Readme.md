## **Цель работы**

Поднять приложение на Django, используя Nginx для обработки статических файлов и прокси используя роли, научиться шаблонизации конфигураций.

**Django-приложение:** [репо](https://github.com/mdn/django-locallibrary-tutorial)

**Docker-образ:** https://hub.docker.com/repository/docker/timurbabs/django

## **Ход работы**

1. Поднять Vagrant-окружение при помощи файла Vagrantfile, используя команду **vagrant up**
2. Написать роль для установки nginx, запушить в репозиторий и добавить в requirements.yml
3. Подготовить в инвентори шаблон конфигурационного файла для Nginx и для default-сайта, с настройками для отдачи статических файлов
4. Учесть в шаблоне проксирование динамики в контейнер с Django
5. Написать плейбук установки через роли nginx на хосты группы web, и docker на хосты группы app

Запустить приложение на хостах группы app или подготовить docker-compose.yml для запуска приложения

## **План раската**

- для начала проверяем, что все прилаги и версии подтянуты из [Readme](https://github.com/justMADx/deusopslabs/blob/main/lab1/Readme.md)

### **Vagrant**
Поднимаем машины

```bash
vagrant up
```

```bash
vagrant status
```
```
Current machine status:

srv1            running(virtualbox)
srv2            running(virtualbox)
```

### **Ansible**

#### **Ansible-galaxy**
Прописываем в wsl 
```bash
ansible-galaxy init nginx
```
, что создаст базовую структуру для роли nginx <br>

После чего создастся директория ```nginx``` <br>
Нам необходимо в: 
- *nginx/defaults/main.yml* создать переменные.
- *nginx/tasks/main.yml* перенести задачи по поднятию nginx и использовать вместо значений переменные из *defaults*.
- *nginx/handlers/main.yml* добавить handler на перезагрузку nginx.
- *nginx/templates/nginx.conf.j2* прописать конфиг для поднятия и прокси nginx на хосты [app].

### *Gitlab*

Запушить в уже созданную группу *nginx*

---
### *Ansible*

### *Ansible-galaxy*

- Создаем файл requirements.yml
- Прописываем в нем имя роли, ссылку на репу с ролью, указываем *main* в качестве src.
- в WSL прописываем 
```bash
ansible-galaxy install -r requirements.yml -f
```
```
Starting galaxy role install process
- changing role docker-role from main to main
- extracting docker-role to /home/jusmad/.ansible/roles/docker-role
- docker-role (main) was installed successfully
- extracting nginx-role to /home/jusmad/.ansible/roles/nginx-role
- nginx-role (main) was installed successfully
```

- Добавляем роль в *docker-playbook.yml* (roles) и запускаем
```bash
ansible-playbook -i hosts.ini docker-playbook.yml
```

В ответе получаем:
<details>

<summary>Ответ</summary>

```html
ok: [192.168.56.201] => {
"django_app_response.content": "<!DOCTYPE html>\n
<html lang=\"en\">\n
<head>\n \n <title>Local Library</title>\n
    <meta charset=\"utf-8\">
    \n
    <meta name=\"viewport\" content=\"width=device-width, initial-scale=1\">
    \n
    <link rel=\"stylesheet\" href=\"https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/css/bootstrap.min.css\"
          integrity=\"sha384-TX8t27EcRE3e/ihU7zmQxVncDAy5uIKz4rEkgIXeMed4M0jlfIDPvg6uqKI2xXr2\"
          crossorigin=\"anonymous\">
    \n\n \n  <!-- Add additional CSS in static file -->\n \n
    <link rel=\"stylesheet\" href=\"/static/css/styles.css\">
    \n
</head>
\n
<body>\n\n
<div class=\"container-fluid\">\n\n
    <div class=\"row\">\n
        <div class=\"col-sm-2\">\n \n
            <ul class=\"sidebar-nav\">\n
                <li><a href=\"/catalog/\">Home</a></li>
                \n
                <li><a href=\"/catalog/books/\">All books</a></li>
                \n
                <li><a href=\"/catalog/authors/\">All authors</a></li>
                \n
            </ul>
            \n \n
            <ul class=\"sidebar-nav\">\n \n
                <li><a href=\"/accounts/login/?next=/catalog/\">Login</a></li>
                \n \n
            </ul>
            \n \n \n \n\n
        </div>
        \n
        <div class=\"col-sm-10 \
        ">\n \n<h1>Local Library Home</h1>\n\n<p>Welcome to <em>LocalLibrary</em>, a very basic Django website developed
            as a
            <a href=\"https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website\">tutorial
                example</a> on the Mozilla Developer Network.</p>\n<p>The tutorial demonstrates how to create a Django
            skeleton website and application, define URL mappings, views (including Generic List and Detail Views),
            models and templates.</p>\n\n\n<h2>UML Models</h2>\n<p>An UML diagram of the site's Django model structure
            is shown below. </p>\n\n
        <div>\n\n<img src=\"/static/images/local_library_model_uml.png\" alt=\"My image\"
            style=\"width:555px;height:540px;\"/>\n
        </div>
        \n\n\n<h2>Dynamic content</h2>\n\n<p>The library has the following record counts:</p>\n
        <ul>\n
            <li><strong>Books:</strong> 0</li>
            \n
            <li><strong>Copies:</strong> 0</li>
            \n
            <li><strong>Copies available:</strong> 0</li>
            \n
            <li><strong>Authors:</strong> 0</li>
            \n
        </ul>
        \n\n\n<p>You have visited this page 1 time.</p>\n\n\n \n \n \n \n \n \n
    </div>
    \n
</div>
\n\n</div>\n
</body>
\n
</html>\n"
}
```

</details>