# Лабораторная работа: Основы Ansible в DevOps.

Выполнил Мухамедов Д.А. ПИ-430Б

### Задание 1: Базовое подключение
1. Установите Ansible на вашей машине
2. Сгенерируйте SSH ключевую пару
3. Создайте инвентарный файл `inventory.ini`
4. Проверьте подключение командой `ansible-inventory --list`
5. Выполните ping к управляемому хосту

**Ожидаемый результат:** успешный ответ "pong" от управляемого хоста

---

### Задание 2: Базовые ad-hoc команды
1. Получите информацию о ядрах CPU управляемого хоста:
   ```bash
   ansible -i inventory.ini managed1 -m setup -a "filter=ansible_processor_cores"
   ```

2. Проверьте свободное место на диске:
   ```bash
   ansible -i inventory.ini managed1 -m command -a "df -h"
   ```

3. Получите список всех пользователей:
   ```bash
   ansible -i inventory.ini managed1 -m command -a "cat /etc/passwd"
   ```

4. Измените временную зону хоста на UTC:
   ```bash
   ansible -i inventory.ini managed1 -m command -a "timedatectl set-timezone UTC"
   ```

**Ожидаемый результат:** вывод команд без ошибок

---

### Задание 3: Работа с файлами
1. Создайте новый playbook `task3_files.yml`:
   ```yaml
   ---
   - name: Work with files
     hosts: managed_hosts
     tasks:
       - name: Create multiple directories
         file:
           path: /tmp/{{ item }}
           state: directory
           mode: '0755'
         loop:
           - test_dir1
           - test_dir2
           - test_dir3
   
       - name: Create files in directories
         copy:
           content: "This is {{ item }} file\n"
           dest: /tmp/{{ item }}/content.txt
         loop:
           - test_dir1
           - test_dir2
           - test_dir3
   
       - name: Display files
         command: cat /tmp/{{ item }}/content.txt
         loop:
           - test_dir1
           - test_dir2
           - test_dir3
         register: file_content
   
       - name: Show file contents
         debug:
           msg: "{{ item.stdout }}"
         loop: "{{ file_content.results }}"
   ```

2. Запустите playbook:
   ```bash
   ansible-playbook -i inventory.ini task3_files.yml
   ```

**Ожидаемый результат:** три директории с файлами, созданные на управляемом хосте

---

## 9. Полезные команды для отладки

### Проверка подключения к контейнеру
```bash
docker-compose ps
docker logs ansible-managed-host
```

### Подключение к контейнеру по SSH с дебаг информацией
```bash
ssh -v -i ~/.ssh/ansible_key -p 2222 ansible@localhost
```

### Перезагрузка контейнера
```bash
docker-compose restart
```

### Удаление контейнера и образа
```bash
docker-compose down
docker-compose rm -f
```

### Запуск playbook с повышенной вербозностью
```bash
ansible-playbook -i inventory.ini playbook.yml -vvv
```

### Синтаксическая проверка playbook
```bash
ansible-playbook -i inventory.ini playbook.yml --syntax-check
```

---

## 10. Часто возникающие проблемы

### Проблема: "Permission denied (publickey)"
**Решение:**
```bash
# Проверьте права на приватный ключ
chmod 600 ~/.ssh/ansible_key

# Убедитесь, что публичный ключ скопирован правильно
docker exec ansible-managed-host cat /home/ansible/.ssh/authorized_keys
```

### Проблема: "No module named 'jinja2'"
**Решение:**
```bash
pip3 install jinja2
ansible-inventory -i inventory.ini --list
```

### Проблема: "Connection refused" на порту 2222
**Решение:**
```bash
# Проверьте, запущен ли контейнер
docker ps

# Пересоздайте контейнер
docker-compose down
docker-compose up -d
```

### Проблема: "UNREACHABLE! => {msg: 'Failed to connect to the host via ssh'"
**Решение:**
1. Проверьте SSH подключение вручную
2. Убедитесь, что SSH сервис запущен в контейнере
3. Проверьте права на файлы в `.ssh`

---

## Дополнительные ресурсы

- [Официальная документация Ansible](https://docs.ansible.com/)
- [Ansible Best Practices](https://docs.ansible.com/ansible/latest/tips_tricks/index.html)
- [Ansible Modules Index](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html)
- [Docker Documentation](https://docs.docker.com/)

---
