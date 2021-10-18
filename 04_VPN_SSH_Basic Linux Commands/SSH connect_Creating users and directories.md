# SSH connect to Debian 10 Server. Creating users, directories and files

## SSH Connect to server

### Open Windows cmd or PowerShell

```cmd
ssh username@server-ip #establish ssh connection as user *username* to server *server-ip*
```

### Create user student

```bash
adduser student
```
or
```bash
useradd -m student
```

### Create directory ~/my_first_directory/ and file ~/my_first_directory/my_first_file.txt

```bash
su student #login as user student
cd ~ (or cd /home/student) #set student`s home folder as work folder
mkdir my_first_directory # create directory named my_first_directory
cd my_first_directory
touch my_first_file.txt #creates empty file named my_first_file.txt
```
