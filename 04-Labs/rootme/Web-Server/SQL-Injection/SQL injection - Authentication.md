[SQL injection - Authentication](https://www.root-me.org/en/Challenges/Web-Server/SQL-injection-authentication)
this is very simple sql injection.
most common thing you learn is put the username then break it with single or double quote then comment the rest.
use this :
username : `admin'--`
password : `its not important`
then you have it but if the input for the password was not showing it fully and be like this :
`.....`
you can inspect and chose the password input and copy the value of the value of that input.