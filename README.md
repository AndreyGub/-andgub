# Домашнее задание к занятию  «Защита хоста» Губайдуллин Андрей Фаритович

### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование этого репозитория к себе на ПК с помощью команды `git clone`.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и ваши фамилию и имя;
   - в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
   - для корректного добавления скриншотов воспользуйтесь инструкцией [«Как вставить скриншот в шаблон с решением»](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md);
   - при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md).
4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`).
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы задавайте в разделе «Вопросы по заданию» в личном кабинете.

Желаем успехов в выполнении домашнего задания.

------

### Задание 1

1. Установите **eCryptfs**.
2. Добавьте пользователя cryptouser.
3. Зашифруйте домашний каталог пользователя с помощью eCryptfs.


*В качестве ответа  пришлите снимки экрана домашнего каталога пользователя с исходными и зашифрованными данными.*  

### Ответ: 

1. Установка eCryptfs и создание пользователя
   
sudo apt update
sudo apt install ecryptfs-utils -y
sudo adduser cryptouser
<img width="640" height="348" alt="image" src="https://github.com/user-attachments/assets/61ca45d2-fa22-4eb8-aac0-0aea91018e56" />

2. Наполним домашний каталог тестовыми данными
   
sudo su - cryptouser
touch file1.txt file2.txt
echo "This is a secret netology" > netology.txt
ls -la
exit


 Исходные данные в /home/cryptouser
 sudo ls -la /home/cryptouser/
 
  <img width="630" height="221" alt="image" src="https://github.com/user-attachments/assets/359a4dad-3638-4ef8-a00f-1c46b5e98dd2" />


3. Зашифруем домашний каталог

sudo ecryptfs-migrate-home -u cryptouser
После завершения миграции домашний каталог становится точкой монтирования, а реальные данные хранятся в зашифрованном виде в /home/.ecryptfs/cryptouser/.Private/.

 Создайте файлы ВНУТРИ Private (зашифрованной области)
cd ~/Private
echo "Это секретный файл пользователя cryptouser" > secret.txt
echo "Конфиденциальные данные для отчета" > report.txt
echo "Логин: admin, Пароль: SuperSecret123" > credentials.txt
Проверим состояние каталога после шифрования :


ls -la /home/cryptouser
<img width="756" height="389" alt="image" src="https://github.com/user-attachments/assets/d56a4260-17e2-49fe-80db-aed6ebcc65f1" />
показывает зашифрованные файлы с именами
<img width="1128" height="142" alt="image" src="https://github.com/user-attachments/assets/2de9e6ba-692b-451e-b162-26f945044e5b" />

### Задание 2

1. Установите поддержку **LUKS**.
2. Создайте небольшой раздел, например, 100 Мб.
3. Зашифруйте созданный раздел с помощью LUKS.

*В качестве ответа пришлите снимки экрана с поэтапным выполнением задания.*

# Ответ: 
# Шаг 1. Установка поддержки LUKS 

sudo apt update
sudo apt install cryptsetup
<img width="625" height="130" alt="image" src="https://github.com/user-attachments/assets/3c9f2d41-efd4-40d9-bf4e-611189d3fb65" />

# Шаг 2. Создаем  раздел 100 Мб
Можно использовать:

# Создаём пустой файл 100 Мб
dd if=/dev/zero of=luks_test.img bs=1M count=100
<img width="773" height="96" alt="image" src="https://github.com/user-attachments/assets/1a6d4819-23d1-4c9f-b2cf-ac9100a172e1" />

# Привязываем как loop-устройство
sudo losetup -f luks_test.img
sudo losetup -l  # посмотреть, какой loop назначен
<img width="664" height="95" alt="image" src="https://github.com/user-attachments/assets/e736db86-b9ea-44ba-84fe-c25deb4e7918" />

# Шаг 3. Шифрование раздела с помощью LUKS
loop-устройство — /dev/loop0.

# Инициализация LUKS
sudo cryptsetup luksFormat /dev/loop0
<img width="771" height="175" alt="image" src="https://github.com/user-attachments/assets/4b34cfad-658f-4cdb-b601-abccfdb6d2be" />

# Открываем зашифрованный раздел

sudo cryptsetup open /dev/loop0 my_encrypted_volume
<img width="785" height="68" alt="image" src="https://github.com/user-attachments/assets/f9ca0007-e89b-4b18-aa05-735b576874d3" />

# Создаем файловую систему
sudo mkfs.ext4 /dev/mapper/my_encrypted_volume
<img width="796" height="172" alt="image" src="https://github.com/user-attachments/assets/d916e4f7-d096-4a8c-844a-5eed9fc964ee" />

# монтируем
sudo mkdir -p /mnt/luks_test
sudo mount /dev/mapper/my_encrypted_volume /mnt/luks_test
<img width="831" height="56" alt="image" src="https://github.com/user-attachments/assets/90933d1d-e9f6-41eb-bed4-b6815ff4bbe2" />

# Проверяем
df -h | grep luks_test
<img width="608" height="41" alt="image" src="https://github.com/user-attachments/assets/7a5bb80f-9d54-4e5c-ad74-b329d6f3c9c6" />


## Дополнительные задания (со звёздочкой*)

Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале

### Задание 3 *

1. Установите **apparmor**.
2. Повторите эксперимент, указанный в лекции.
3. Отключите (удалите) apparmor.


*В качестве ответа пришлите снимки экрана с поэтапным выполнением задания.*


