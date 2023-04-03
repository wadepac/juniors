## Jenkins

#### Установка и настройка Jenkins

1. Добавим необходимые ключи и репозитории:
```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
    /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
```

2. Установим пакеты:
```
sudo apt-get update
sudo apt-get install fontconfig openjdk-11-jre
sudo apt-get install jenkins
```

3. Запустим сервис и проверим статус
```
sudo systemctl start jenkins
sudo systemctl status jenkins
```

4. Далее перейдем в браузер по адерсу ip-jenkins:8080 
<img width="986" alt="install-jenkins-web-1" src="https://user-images.githubusercontent.com/66735783/229451127-ce061bbc-8f5f-436d-8eaa-8772be752779.png">


5. Чтобы узнать пароль введем в терминале сервера:
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

6. Скопируем полученное значение и вставим в окно в браузере
7. Продолжим установку и выберем Install suggested plugins
<img width="933" alt="install-jenkins-web-2" src="https://user-images.githubusercontent.com/66735783/229451232-83bef215-eb75-468a-bd65-1129babafc7b.png">

8. Запустится установка
<img width="955" alt="install-jenkins-web-3" src="https://user-images.githubusercontent.com/66735783/229451304-3c738279-ebdc-40b9-a6bd-a41e84010f46.png">

9. По окончанию установки необходимо создать первого пользователя с админскими правами. Создадим пользователя jenkadm с паролем Je9k19s и адресом почты jenkadm@test.local:
<img width="920" alt="install-jenkins-web-4" src="https://user-images.githubusercontent.com/66735783/229452347-fdeb5b50-eca7-4760-bc94-b952913a8fc0.png">

10. Для завершения установки укажем url доступа в формате https://ip-jenkins:8080 :
<img width="974" alt="install-jenkins-web-4" src="https://user-images.githubusercontent.com/66735783/229452417-5c9770d4-d4e9-4e7b-9443-a0eb36799c5d.png">

11. При завершении установки без ошибок мы увидим следующее окно:
<img width="968" alt="install-jenkins-web-5" src="https://user-images.githubusercontent.com/66735783/229452924-171cdb4c-ae8a-4c1c-ad30-7f7560a38294.png">

#### Установка Docker
