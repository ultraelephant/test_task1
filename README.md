# test_task1

Для запуска и провижининга хостов необходимо выполнить команду ````vagrant up```` находясь в каталоге репозитория.


## Переменные Vagrantfile

VM_VRRP_COUNT - количество хостов, для которых будет применено vrrp

VM_COUNT - общее количество хостов

**VM_VRRP_COUNT не должно быть больше VM_COUNT**



## Переменные minio.yml

tenant_list - количество экземпляров minio, записывается в виде:
```yaml
- name: "название первого экземпляра"
  int: "1"
- name: "название второго экземпляра"
  int: "2"
 ```

Экземпляры нумеруются от 1 до 9 (то есть максимально возможное количество экземпляров на данный момент - 9)





## ad-hoc комманды ansible

Все комманды запускаются из папки в которой лежит Vagrantfile

**Создание пользователя с правом на чтение каталогов MinIO (***/usr/local/share/{имя каталога}***)**
```bash
ansible all -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory -u vagrant --private-key ~/.vagrant.d/insecure_private_key -m user -a "name=minioreader" --become
```
Создаётся пользователь входящий только в свою группу. Данный пользователь имеет минимальный набор прав, в том числе чтение из каталога MinIO

**Создание пользователя с правом на запись каталогов MinIO (***/usr/local/share/{имя каталога}***)**
```bash
ansible all -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory -u vagrant --private-key ~/.vagrant.d/insecure_private_key -m user -a "name=miniowriter groups=minio-user" --become
```
Создаётся пользователь входящий в свою группу и группу minio-user. Пользователи входящие в данную группу имею доступ на запись в каталоги MinIO.

**Залить jpg файл**

Создаём бакет с именем test. Айтентификационные данные соотвествуют тем, что были созданны из шаблона *minio.j2*
```bash
AWS_ACCESS_KEY=admin AWS_SECRET_KEY=password ansible localhost -m aws_s3 -a "bucket=test mode=create permission=public-read s3_url=http://192.168.0.11:9002""
```
Заливаем файл на созданный бакет. Айтентификационные данные соотвествуют тем, что были созданны из шаблона *minio.j2*
```bash
AWS_ACCESS_KEY=admin AWS_SECRET_KEY=password ansible localhost -m aws_s3 -a "bucket=test object='/test (1).jpg' src='./test (1).jpg' mode=put encrypt=no s3_url=http://192.168.0.11:9002"
```
