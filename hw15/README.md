# **Домашнее задание №15: разворачиваем сетевую лабораторию**

## **Задание:**
# otus-linux
Vagrantfile - для стенда урока 9 - Network

# Дано
https://github.com/erlong15/otus-linux/tree/network
(ветка network)

Vagrantfile с начальным построением сети
- inetRouter
- centralRouter
- centralServer

тестировалось на virtualbox

# Планируемая архитектура
построить следующую архитектуру

Сеть office1
- 192.168.2.0/26 - dev
- 192.168.2.64/26 - test servers
- 192.168.2.128/26 - managers
- 192.168.2.192/26 - office hardware

Сеть office2
- 192.168.1.0/25 - dev
- 192.168.1.128/26 - test servers
- 192.168.1.192/26 - office hardware


Сеть central
- 192.168.0.0/28 - directors
- 192.168.0.32/28 - office hardware
- 192.168.0.64/26 - wifi

```
Office1 ---\
-----> Central --IRouter --> internet
Office2----/
```
Итого должны получится следующие сервера
- inetRouter
- centralRouter
- office1Router
- office2Router
- centralServer
- office1Server
- office2Server

# Теоретическая часть
- Найти свободные подсети
- Посчитать сколько узлов в каждой подсети, включая свободные
- Указать broadcast адрес для каждой подсети
- проверить нет ли ошибок при разбиении

# Практическая часть
- Соединить офисы в сеть согласно схеме и настроить роутинг
- Все сервера и роутеры должны ходить в инет черз inetRouter
- Все сервера должны видеть друг друга
- у всех новых серверов отключить дефолт на нат (eth0), который вагрант поднимает для связи
- при нехватке сетевых интервейсов добавить по несколько адресов на интерфейс


Критерии оценки: 4 - сделана хотя бы часть
5 - все сделано

## **Выполнено:**

## Теоретическая часть

1.Для сети office1

|   Сеть office1  |    Адрес подсети |   HostMin,HostMax,Hosts/Net      |   Broadcast   |           Адрес подсети (binary)       |
|:---------------:|:----------------:|:--------------------------------:|:-------------:|:--------------------------------------:|
| dev             | 192.168.2.0/26   | 192.168.2.1   - 192.168.2.62,62  | 192.168.2.63  | 11000000.10101000.00000010.00 000000   |
| test servers    | 192.168.2.64/26  | 192.168.2.65  - 192.168.2.126,62 | 192.168.2.127 | 11000000.10101000.00000010.01 000000   |
| managers        | 192.168.2.128/26 | 192.168.2.129 - 192.168.2.190,62 | 192.168.2.191 | 11000000.10101000.00000010.10 000000   |
| office hardware | 192.168.2.192/26 | 192.168.2.193 - 192.168.2.254,62 | 192.168.2.255 | 11000000.10101000.00000010.11 000000   |


Свободных подсетей - нет. Ошибок разбиения - нет.

2.Для сети office2

|   Сеть office2  |    Адрес подсети |   HostMin,HostMax,Hosts/Net       |   Broadcast   |           Адрес подсети (binary)       |
|:---------------:|:----------------:|:---------------------------------:|:-------------:|:--------------------------------------:|
| dev             | 192.168.1.0/25   |  192.168.1.1 - 192.168.1.126,126  | 192.168.1.127 | 11000000.10101000.00000001.0 0000000   |
| test servers    | 192.168.1.128/26 |  192.168.1.129 - 192.168.1.190,62 | 192.168.1.191 | 11000000.10101000.00000001.10 000000   |
| office hardware | 192.168.1.192/26 |  192.168.1.129 - 192.168.1.190,62 | 192.168.1.255 | 11000000.10101000.00000001.11 000000   |

Свободных подсетей - нет. Ошибок разбиения - нет.

3.Для сети central

|   Сеть central  |   Адрес подсети |  HostMin,HostMax,Hosts/Net      |   Broadcast   |           Адрес подсети (binary)       |
|:---------------:|:---------------:|:-------------------------------:|:-------------:|:--------------------------------------:|
| directors       | 192.168.0.0/28  | 192.168.0.1 - 192.168.0.14,14   | 192.168.0.15  | 11000000.10101000.00000000.0000 0000   |
| office hardware | 192.168.0.32/28 | 192.168.0.33 - 192.168.0.46,14  | 192.168.0.47  | 11000000.10101000.00000000.0010 0000   |
| wifi            | 192.168.0.64/26 | 192.168.0.65 - 192.168.0.126,62 | 192.168.0.127 | 11000000.10101000.00000000.01 000000   |

Явных ошибок разбиения - нет. Для оптимизации свободного адресного пространства под будущие подсети лучше было бы спланировать сеть следующим образом (начиная с наибольшей по количеству хостов):

|   Сеть central  |   Адрес подсети | HostMin,HostMax,Hosts/Net      |   Broadcast  |           Адрес подсети (binary)       |
|:---------------:|:---------------:|:------------------------------:|:------------:|:--------------------------------------:|
| wifi            | 192.168.0.0/26  | 192.168.0.1 - 192.168.0.62,62  | 192.168.0.63 | 11000000.10101000.00000000.00 000000   |
| directors       | 192.168.0.64/28 | 192.168.0.65 - 192.168.0.78,14 | 192.168.0.79 | 11000000.10101000.00000000.0100 0000   |
| office hardware | 192.168.0.80/28 | 192.168.0.81 - 192.168.0.94,14 | 192.168.0.95 | 11000000.10101000.00000000.0101 0000   |


**Свободные подсети central:**

| Адрес подсети   | HostMin,HostMax,Hosts/Net      | Broadcast    | Адрес подсети (binary)               |
|-----------------|--------------------------------|--------------|--------------------------------------|
| 192.168.0.16/28 | 192.168.0.17 - 192.168.0.30,14 | 192.168.0.31 | 11000000.10101000.00000000.0001 0000 |
| 192.168.0.16/29 | 192.168.0.17 - 192.168.0.22,6  | 192.168.0.23 | 11000000.10101000.00000000.00010 000 |
| 192.168.0.24/29 | 192.168.0.25 - 192.168.0.30,6  | 192.168.0.31 | 11000000.10101000.00000000.00011 000 |
| 192.168.0.16/30 | 192.168.0.17 - 192.168.0.18,2  | 192.168.0.19 | 11000000.10101000.00000000.000100 00 |
| 192.168.0.20/30 | 192.168.0.21 - 192.168.0.22,2  | 192.168.0.23 | 11000000.10101000.00000000.000101 00 |
| 192.168.0.24/30 | 192.168.0.25 - 192.168.0.26,2  | 192.168.0.27 | 11000000.10101000.00000000.000110 00 |
| 192.168.0.28/30 | 192.168.0.29 - 192.168.0.30,2  | 192.168.0.31 | 11000000.10101000.00000000.000111 00 |


| Адрес подсети   | HostMin,HostMax,Hosts/Net       | Broadcast    | Адрес подсети (binary)               |
|-----------------|---------------------------------|--------------|--------------------------------------|
| 192.168.0.48/28 |  192.168.0.49 - 192.168.0.62,14 | 192.168.0.63 | 11000000.10101000.00000000.0011 0000 |
| 192.168.0.48/29 |  192.168.0.49 - 192.168.0.54,6  | 192.168.0.55 | 11000000.10101000.00000000.00110 000 |
| 192.168.0.56/29 |  192.168.0.57 - 192.168.0.62,6  | 192.168.0.63 | 11000000.10101000.00000000.00111 000 |
| 192.168.0.48/30 |  192.168.0.49 - 192.168.0.50,2  | 192.168.0.51 | 11000000.10101000.00000000.001100 00 |
| 192.168.0.52/30 |  192.168.0.53 - 192.168.0.54,2  | 192.168.0.55 | 11000000.10101000.00000000.001101 00 |
| 192.168.0.56/30 |  192.168.0.57 - 192.168.0.58,2  | 192.168.0.59 | 11000000.10101000.00000000.001110 00 |
| 192.168.0.60/30 |  192.168.0.29 - 192.168.0.30,2  | 192.168.0.63 | 11000000.10101000.00000000.001111 00 |



| Адрес подсети   | HostMin - HostMax,Hosts/Net         | Broadcast    | Адрес подсети (binary)               |
|-----------------|-------------------------------------|--------------|--------------------------------------|
|192.168.0.128/25 | 192.168.0.129 - 192.168.0.254,126	| 192.168.0.255| 11000000.10101000.00000000.1 0000000 |
|192.168.0.128/26 | 192.168.0.129 - 192.168.0.190,62	| 192.168.0.191| 11000000.10101000.00000000.10 000000 |
|192.168.0.192/26 | 192.168.0.193 - 192.168.0.254,62	| 192.168.0.255| 11000000.10101000.00000000.11 000000 |
|192.168.0.128/27 | 192.168.0.129 - 192.168.0.158,30	| 192.168.0.159| 11000000.10101000.00000000.100 00000 |
|192.168.0.160/27 | 192.168.0.161 - 192.168.0.190,30	| 192.168.0.191| 11000000.10101000.00000000.101 00000 |
|192.168.0.192/27 | 192.168.0.193 - 192.168.0.222,30	| 192.168.0.223| 11000000.10101000.00000000.110 00000 |
|192.168.0.224/27 | 192.168.0.225 - 192.168.0.254,30	| 192.168.0.255| 11000000.10101000.00000000.111 00000 |
|192.168.0.128/28 | 192.168.0.128 - 192.168.0.142,14	| 192.168.0.143| 11000000.10101000.00000000.1000 0000 |
|192.168.0.144/28 | 192.168.0.145 - 192.168.0.159,14	| 192.168.0.159| 11000000.10101000.00000000.1001 0000 |
|192.168.0.160/28 | 192.168.0.161 - 192.168.0.174,14	| 192.168.0.175| 11000000.10101000.00000000.1010 0000 |
|192.168.0.176/28 | 192.168.0.177 - 192.168.0.190,14	| 192.168.0.191| 11000000.10101000.00000000.1000 0000 |
|192.168.0.192/28 | 192.168.0.193 - 192.168.0.206,14	| 192.168.0.207| 11000000.10101000.00000000.1100 0000 |
|192.168.0.208/28 | 192.168.0.209 - 192.168.0.222,14	| 192.168.0.223| 11000000.10101000.00000000.1101 0000 |
|192.168.0.224/28 | 192.168.0.225 - 192.168.0.238,14	| 192.168.0.239| 11000000.10101000.00000000.1110 0000 |
|192.168.0.240/28 | 192.168.0.241 - 192.168.0.254,14	| 192.168.0.255| 11000000.10101000.00000000.1111 0000 |
|192.168.0.128/29 | 192.168.0.129 - 192.168.0.130,6	| 192.168.0.135| 11000000.10101000.00000000.10000 000 |
|192.168.0.136/29 | 192.168.0.137 - 192.168.0.142,6	| 192.168.0.143| 11000000.10101000.00000000.10001 000 |
|192.168.0.144/29 | 192.168.0.145 - 192.168.0.150,6	| 192.168.0.151| 11000000.10101000.00000000.10010 000 |
|192.168.0.152/29 | 192.168.0.153 - 192.168.0.158,6	| 192.168.0.159| 11000000.10101000.00000000.10011 000 |
|192.168.0.160/29 | 192.168.0.161 - 192.168.0.166,6	| 192.168.0.167| 11000000.10101000.00000000.10100 000 |
|192.168.0.168/29 | 192.168.0.169 - 192.168.0.174,6	| 192.168.0.175| 11000000.10101000.00000000.10101 000 |
|192.168.0.176/29 | 192.168.0.176 - 192.168.0.182,6	| 192.168.0.183| 11000000.10101000.00000000.10110 000 |
|192.168.0.184/29 | 192.168.0.185 - 192.168.0.190,6	| 192.168.0.191| 11000000.10101000.00000000.10111 000 |
|192.168.0.192/29 | 192.168.0.193 - 192.168.0.198,6	| 192.168.0.199| 11000000.10101000.00000000.11000 000 |
|192.168.0.200/29 | 192.168.0.201 - 192.168.0.206,6	| 192.168.0.207| 11000000.10101000.00000000.11001 000 |
|192.168.0.208/29 | 192.168.0.209 - 192.168.0.214,6	| 192.168.0.215| 11000000.10101000.00000000.11010 000 |
|192.168.0.216/29 | 192.168.0.217 - 192.168.0.222,6	| 192.168.0.223| 11000000.10101000.00000000.11011 000 |
|192.168.0.224/29 | 192.168.0.225 - 192.168.0.230,6	| 192.168.0.231| 11000000.10101000.00000000.11100 000 |
|192.168.0.232/29 | 192.168.0.233 - 192.168.0.238,6	| 192.168.0.239| 11000000.10101000.00000000.11101 000 |
|192.168.0.240/29 | 192.168.0.241 - 192.168.0.246,6	| 192.168.0.247| 11000000.10101000.00000000.11110 000 |
|192.168.0.248/29 | 192.168.0.249 - 192.168.0.254,6	| 192.168.0.255| 11000000.10101000.00000000.11111 000 |
|192.168.0.128/30 | 192.168.0.129 - 192.168.0.130,2	| 192.168.0.131| 11000000.10101000.00000000.100000 00 |
|192.168.0.132/30 | 192.168.0.131 - 192.168.0.132,2	| 192.168.0.135| 11000000.10101000.00000000.100001 00 |
|192.168.0.136/30 | 192.168.0.137 - 192.168.0.138,2	| 192.168.0.139| 11000000.10101000.00000000.100010 00 |
|192.168.0.140/30 | 192.168.0.141 - 192.168.0.142,2	| 192.168.0.143| 11000000.10101000.00000000.100011 00 |
|192.168.0.144/30 | 192.168.0.145 - 192.168.0.146,2	| 192.168.0.147| 11000000.10101000.00000000.100100 00 |
|192.168.0.148/30 | 192.168.0.149 - 192.168.0.150,2	| 192.168.0.151| 11000000.10101000.00000000.100101 00 |
|192.168.0.152/30 | 192.168.0.153 - 192.168.0.154,2	| 192.168.0.155| 11000000.10101000.00000000.100110 00 |
|192.168.0.156/30 | 192.168.0.157 - 192.168.0.158,2	| 192.168.0.159| 11000000.10101000.00000000.100111 00 |
|192.168.0.160/30 | 192.168.0.161 - 192.168.0.162,2	| 192.168.0.163| 11000000.10101000.00000000.101000 00 |
|192.168.0.164/30 | 192.168.0.165 - 192.168.0.166,2	| 192.168.0.167| 11000000.10101000.00000000.101001 00 |
|192.168.0.168/30 | 192.168.0.169 - 192.168.0.170,2	| 192.168.0.171| 11000000.10101000.00000000.101010 00 |
|192.168.0.172/30 | 192.168.0.173 - 192.168.0.174,2	| 192.168.0.175| 11000000.10101000.00000000.101011 00 |
|192.168.0.176/30 | 192.168.0.177 - 192.168.0.178,2	| 192.168.0.179| 11000000.10101000.00000000.101100 00 |
|192.168.0.180/30 | 192.168.0.181 - 192.168.0.182,2	| 192.168.0.183| 11000000.10101000.00000000.101101 00 |
|192.168.0.184/30 | 192.168.0.185 - 192.168.0.186,2	| 192.168.0.187| 11000000.10101000.00000000.101110 00 |
|192.168.0.188/30 | 192.168.0.189 - 192.168.0.190,2	| 192.168.0.191| 11000000.10101000.00000000.101111 00 |
|192.168.0.192/30 | 192.168.0.193 - 192.168.0.194,2	| 192.168.0.195| 11000000.10101000.00000000.110000 00 |
|192.168.0.196/30 | 192.168.0.197 - 192.168.0.198,2	| 192.168.0.199| 11000000.10101000.00000000.110001 00 |
|192.168.0.200/30 | 192.168.0.201 - 192.168.0.202,2	| 192.168.0.203| 11000000.10101000.00000000.110010 00 |
|192.168.0.204/30 | 192.168.0.205 - 192.168.0.206,2	| 192.168.0.207| 11000000.10101000.00000000.110011 00 |
|192.168.0.208/30 | 192.168.0.209 - 192.168.0.210,2	| 192.168.0.211| 11000000.10101000.00000000.110100 00 |
|192.168.0.212/30 | 192.168.0.213 - 192.168.0.214,2	| 192.168.0.215| 11000000.10101000.00000000.110101 00 |
|192.168.0.216/30 | 192.168.0.216 - 192.168.0.217,2	| 192.168.0.219| 11000000.10101000.00000000.110110 00 |
|192.168.0.220/30 | 192.168.0.221 - 192.168.0.222,2	| 192.168.0.223| 11000000.10101000.00000000.110111 00 |
|192.168.0.224/30 | 192.168.0.225 - 192.168.0.226,2	| 192.168.0.227| 11000000.10101000.00000000.111000 00 |
|192.168.0.228/30 | 192.168.0.229 - 192.168.0.230,2	| 192.168.0.231| 11000000.10101000.00000000.111001 00 |
|192.168.0.232/30 | 192.168.0.233 - 192.168.0.234,2	| 192.168.0.235| 11000000.10101000.00000000.111010 00 |
|192.168.0.236/30 | 192.168.0.237 - 192.168.0.238,2	| 192.168.0.239| 11000000.10101000.00000000.111011 00 |
|192.168.0.240/30 | 192.168.0.241 - 192.168.0.242,2	| 192.168.0.243| 11000000.10101000.00000000.111100 00 |
|192.168.0.244/30 | 192.168.0.245 - 192.168.0.246,2	| 192.168.0.247| 11000000.10101000.00000000.111101 00 |
|192.168.0.248/30 | 192.168.0.249 - 192.168.0.250,2	| 192.168.0.251| 11000000.10101000.00000000.111110 00 |
|192.168.0.252/30 | 192.168.0.253 - 192.168.0.254,2	| 192.168.0.255| 11000000.10101000.00000000.111111 00 |



## Практическая часть

В ходе выполнения практической части подготовлен [Vagrantfile](Vagrantfile)

Построена следующая сеть:
```
Office1Server--->Office1Router ---\
CentralServer------------> CentralRouter --InetRouter --> internet
Office2Server--->Office2Router ---/
```

![Схема сети](https://github.com/Stump-D/hws_otuslinux/blob/master/hw15/net15.png)


| Узел          | Интерфейс | Адрес                              | Default GW                   | virtualbox__intnet |
|---------------|-----------|------------------------------------|------------------------------|--------------------|
| inetRouter    | eth0      | DHCP                               | DHCP                         |                    |
|               | eth1      | 192.168.255.1/255.255.255.252 =30  |                              | "router-net"       |
| centralRouter | eth1      | 192.168.255.2/255.255.255.252 =30  |  192.168.255.1 (inetRouter)  | "router-net"       |
|               | eth2      | 192.168.0.1/255.255.255.240 = 28   |                              | "directors-net"    |
|               | eth3      | 192.168.0.33/255.255.255.240 = 28  |                              | "officehw-net"     |
|               | eth4      | 192.168.0.65/255.255.255.192 = 26  |                              | "wifi-net"         |
|               | eth5      | 192.168.0.17/255.255.255.252 = 30  |                              | "office1-net"      |
|               | eth6      | 192.168.0.21/255.255.255.252 = 30  |                              | "office2-net"      |
| centralServer | eth1      | 192.168.0.2/255.255.255.240 = 28   | 192.168.0.1(CentralRouter)   | "directors-net"    |
| office1Router | eth1      | 192.168.0.18/255.255.255.252 = 30  | 192.168.0.17(CentralRouter)  | "office1-net"      |
|               | eth2      | 192.168.2.1/255.255.255.192 = 26   |                              | "dev1-net"         |
|               | eth3      | 192.168.2.65/255.255.255.192 = 26  |                              | "testservers1-net" |
|               | eth4      | 192.168.2.129/255.255.255.192 = 26 |                              | "managers1-net"    |
|               | eth5      | 192.168.2.193/255.255.255.192 = 26 |                              | "officehw1-net"    |
| office1Server | eth1      | 192.168.2.66/255.255.255.192 = 26  | 192.168.2.65(Office1Router)  | "testservers1-net" |
| office2Router | eth1      | 192.168.0.22/255.255.255.252 = 30  | 192.168.0.21(CentralRouter)  | "office2-net"      |
|               | eth2      | 192.168.1.1/255.255.255.128 = 25   |                              | "dev2-net"         |
|               | eth3      | 192.168.1.129/255.255.255.192 = 26 |                              | "testservers2-net" |
|               | eth4      | 192.168.1.193/255.255.255.192 = 26 |                              | "officehw2-net"    |
| office2Server | eth1      | 192.168.1.130/255.255.255.192 = 26 | 192.168.1.129(office2Router) | "testservers2-net" |
