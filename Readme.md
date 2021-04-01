# Исходники сайта [matematika.org](https://matematika.org)

<br/>

### Запустить matematika.org для разработки

    $ docker-compose up

<br/>

### Запустить matematika.org локально как сервис

<a href="https://sysadm.ru/linux/servers/containers/docker/install/">Docker</a> должен быть установлен.

    # cp matematika.org.service /etc/systemd/system/matematika.org.service

<br/>

    # systemctl enable matematika.org.service
    # systemctl start  matematika.org.service
    # systemctl status matematika.org.service

http://localhost:4018
