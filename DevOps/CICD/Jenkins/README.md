## Jenkins

#### Установка Jenkins

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

5. Чтобы узнать пароль введем в терминале сервера:
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

6. Скопируем полученное значение и вставим в окно в браузере
7. Продолжим установку и выберем Install suggested plugins

8. Запустится установка