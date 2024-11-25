## **Цель работы**

Научиться пользоваться Ansible Galaxy и писать роли

## **Рекомендуемая документация**

- [Инфраструктура как код #3: Ansible-роли и Galaxy](https://www.youtube.com/watch?v=sEzoDAfLstw)
- [Документация по Ansible Galaxy](https://docs.ansible.com/ansible/latest/galaxy/user_guide.html)

## **Ход работы**

1. Инициировать структуру роли “**Docker**” через Ansible-Galaxy
2. Вынести из предыдущего плейбука задачи установки Docker в отдельную роль
3. Параметризовать роль через переменные
4. Создать в gitlab группу для репозиториев с ролями, запушить роль “**Docker**” в репозиторий
5. Создать файл requirements.yml для установки роли Docker из репозитория
6. Запустить приложение на нодах группы [app] используя ansible-playbook с ролью “**Docker**”


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
ansible-galaxy init docker
```
, что создаст базовую структуру для роли docker <br>

После чего создастся директория ```docker``` <br>
Нам необходимо в: 
- *docker/defaults/main.yml* создать переменные.
- *docker/tasks/main.yml* перенести задачи по поднятию докера и использовать вместо значений переменные из *defaults*.

### *Gitlab*

Нужно создать группу в Gitlab -> создать проект с ролью -> запушить туда *docker*

---
### *Ansible*

### *Ansible-galaxy*

- Создаем файл requirements.yml
- Прописываем в нем имя роли, ссылку на репу с ролью, указываем *main* в качестве src.
- в WSL прописываем 
```bash
ansible-galaxy install -r requirements.yml
```
```
Starting galaxy role install process
- extracting deusops-role to /home/jusmad/.ansible/roles/deusops-role
- deusops-role (main) was installed successfully
```

- Добавляем роль в *docker-playbook.yml* (roles) и запускаем
```bash
ansible-playbook -i hosts.ini docker-playbook.yml
```

