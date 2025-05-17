primeiro comando lab CAP


comando

└─# nmap -p- -Pn -min-rate 300 -oG Allports 172.16.8.24

resultado

Not shown: 65531 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
111/tcp  open  rpcbind
3306/tcp open  mysql

