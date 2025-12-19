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

## Скрины

1. Базовое подключение
<img width="904" height="599" alt="image" src="https://github.com/user-attachments/assets/50542550-14fd-4e8c-bbed-4ef5a89b51fc" />
<img width="631" height="111" alt="image" src="https://github.com/user-attachments/assets/71cff2bf-ef13-4699-83f5-5c28cac6b975" />

2. Информация о ядрах CPU управляемого хоста
<img width="873" height="138" alt="image" src="https://github.com/user-attachments/assets/39cfdaae-b19b-4376-b5a0-4e8a259b8f83" />

3. Проверка свободного места на диске
<img width="714" height="161" alt="image" src="https://github.com/user-attachments/assets/45da15b7-3346-41c7-8138-fb5f3ed0e579" />

4. Список пользователей
<img width="777" height="454" alt="image" src="https://github.com/user-attachments/assets/df6c7066-773c-4859-a331-1baabf887741" />

5. Изменение временной зоны на UTC
<img width="1036" height="124" alt="image" src="https://github.com/user-attachments/assets/06344cf5-c6c5-442c-9a3f-495c933515cc" />

6. Работа с файлами
<img width="1594" height="387" alt="image" src="https://github.com/user-attachments/assets/a1f8a7d0-3384-4a16-aac2-009c8dfc5268" />


Ansible — это инструмент для автоматизации администрирования и DevOps-задач: настройки серверов, установки ПО, управления конфигурациями и деплоя приложений. Проще говоря - Ansible позволяет описать нужное состояние системы (что должно быть установлено и как настроено), а затем автоматически привести серверы к этому состоянию.
