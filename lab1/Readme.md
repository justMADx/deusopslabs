## **Цель работы**

Запустить приложение при помощи ansible, научиться писать плейбуки и пользоваться group vars

**Docker-образ:** https://hub.docker.com/repository/docker/timurbabs/django

Универсальный файл для вагранта: [Vagrantfile](https://drive.google.com/file/d/1Q5deuz9kcm9VeXDiX44iuHZZSp66VuAY/view?usp=share_link)

## **Рекомендуемая документация**

- [Инфраструктура как код #1: понятие инфраструктурного кода (Лекция)](https://www.youtube.com/watch?v=RS9fAJM0tf0&ab_channel=DeusOps)
- [Инфраструктура как код #2: Знакомство с Ansible (Лекция)](https://youtu.be/GFL6-DlSQH0)
- [Как поднимать виртуальные машины в Vagrant](https://www.youtube.com/watch?v=dgm5MtCcIMs&t=5150s)
- [Документация Ansible](https://docs.ansible.com/ansible/latest/user_guide/index.html#writing-tasks-plays-and-playbooks)
- [Подборка по Ansible](https://gitlab.com/deusops/lessons/documentation/ansible)

## **Ход работы**

1. Установить Ansible используя ([Гайд по установке](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html))
2. Установить Vagrant ([Гайд по установке](https://developer.hashicorp.com/vagrant/downloads))
3. Поднять Vagrant хост из ссылки при помощи файла Vagrantfile используя команду ***vagrant up** ([VagrantFile](https://drive.google.com/drive/u/0/folders/1Ev8N8LijxNR2npEwhoUFlxBuznf--ujP))
4. Написать inventory-файл для развернутых хостов
5. Написать плейбук для установки Docker
6. Клонировать репо ([Репозиторий](https://github.com/mdn/django-locallibrary-tutorial)) на ноды группы [app]
7. Запустить приложение на нодах группы [app] используя ansible
---
## **План раската**

### **Vagrant**
Устанавливаем на свою машину Vagrant с официального сайта.
- проверяем установка при помощи <code>vagrant --version</code>
- переходим в папку, где лежит *Vagrantfile* <code>/path/to/vagrantfile</code>
- прописываем <code>vagrant up</code> и ждем, пока поднимутся виртуалки и туда подтянутся образы *ubuntu*.
- проверяем при помощи <code>vagrant status</code>
```
vagrant status
Current machine status:

srv1            running(virtualbox)
srv2            running(virtualbox)
```
(не забываем установить virtualbox для работы vagrant) 

### **Ansible**
Так как *ansible* не поддерживается на Windows, то для его использования нужно установить wsl при помощи <code>wsl --install</code><br>
*посмотреть список доступных дистрибутивов можно при помощи <code>wsl -l -o</code><br>
- после установки wsl (я выбрал ubuntu), переходим туда при помощи <code>ubuntu</code>
- для установки *ansible* используем
```
sudo apt update
sudo apt install ansible
```
- проверяем при помощи
```
~$ ansible --version
ansible [core 2.16.3]
  config file = None
  configured module search path = ['/home/jusmad/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/jusmad/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.12.3 (main, Sep 11 2024, 14:17:37) [GCC 13.2.0] (/usr/bin/python3)
  jinja version = 3.1.2
  libyaml = True
```
- для текущей лабораторной потребуется **docker-playbook.yml** и **hosts.ini**. <br>
**hosts.ini** - содержит конфиг для группы хостов и их свойства. <br>
**docker-playbook.yml** - содержит список выполняемых задач на каждом из хостов, указанных в *hosts.ini*.
<br>
<br>
- при помощи <code>ansible all -i hosts.ini -m ping</code> пингуем наши машины, поднятые при помощи *vagrant* и ожидаем ответа **pong** от каждой из них.<br>
- в случае успеха запускаем задачи из playbook для каждого хоста из hosts.ini: <code>ansible-playbook -i hosts.ini docker-playbook.yml</code>, дожидаемся раската конфигов на машинах и ответа от сервера.<br>

## F.A.Q
### проблемы, которые могут возникнуть:
1. во время поднятия <code>vagrant up</code> вылетает timeout ошибка
- не хватает мощности/памяти/скорости интернета для подтягивания образа ubuntu и поднятия машин. <br>
**Решение:** необходимо увеличить timeout в *Vagrantfile*, повысив значение переменной
<code>config.vm.boot_timeout</code> (моему старичку хватило 10 минут для поднятия).

2. во время попытки пинговать выдает ошибку с *ssh* ключами *UNPROTECTED PRIVATE KEYS* или что-то в этом духе.
- у ключей слишком открытый доступ и его нужно понизить. <br>
**Решение:** есть два решения:<br><br>
  a. в /etc/wsl.conf добавить поле
  ```
  [automount]
  options="metadata"
  ```
  WSL будет автоматически монтировать метаданные: которые включают информацию о владельце, группе, правах доступа и другие атрибуты файлов и директорий.

  После этого перезапускаем *wsl*, переходим в <code>/mnt/path/to/.vagrant/machines/srv1(2)/virtualbox</code> и прописываем на каждый из приватников <code>chmod 600</code>:<br>
   владелец файла (user) имеет права на чтение и запись, а группа и остальные пользователи не имеют прав доступа.<br>

   b. через wsl скопировать файлы из винды на wsl и поменять права там. <br><br>
    Проверяем при помощи <code>ls -l private_key</code> права на ключи, и если ответ <code>-rw-------</code>, то все получилось.<br>
После этого ошибка с ключами ssh должна пройти.

3. во время поднятия прилаги на Django из playbook вылетает ошибка *Not supported URL scheme http+docker*: 
- скорее всего *requests* установились не той версии, для проверки нужно зайти на одну из машин и проверить версию при помощи 
```
python3 -c "import requests; print(requests.__version__)"

2.31.0(должна быть)
#если версия >= 2.32.0, то ошибка так и будет вылетать в связи с новыми стандартами
``` 
**Решение:** если через playbook установка requests нужной версии все таки не прошла, то необходимо ручками подтянуть в каждую из машин версию *requests* < 2.32.0 

