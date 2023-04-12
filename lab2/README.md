# Сбор и аналитическая обработка информации о сетевом трафике

## Цель работы

1. Развить практические навыки использования современного стека инструментов сбора и аналитической обработки информации о сетевом трафике.

2. Освоить базовые подходы блокировки нежелательного сетевого трафика.

3. Закрепить знания о современных сетевых протоколах прикладного уровня.

## Ход работы

1. C помощью Wireshark был собран сетевой трафик объёмом 105 Мб и записан в файл data.pcapng.

![Image alt]([https://github.com/vikulek/tmp/blob/main/%D0%9F%D1%80%D0%B0%D0%BA%D1%82%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B0%D1%8F%202/posl.jpg](https://github.com/vikulek/SAIZOND/blob/main/lab2/screen/screen.png))

2. С помощью Zeek была выделена метаинформация сетевого трафика (файлы dns.log и http.log).

3. Затем получаю список нежелательного трафика:

```{bash}
mkdir hosts
wget -q https://github.com/StevenBlack/hosts/raw/master/data/add.2o7Net/hosts -O hosts/hosts.1
wget -q https://raw.githubusercontent.com/StevenBlack/hosts/master/data/KADhosts/hosts -O hosts/hosts.2
wget -q https://raw.githubusercontent.com/StevenBlack/hosts/master/data/yoyo.org/hosts -O hosts/hosts.3
wget -q https://raw.githubusercontent.com/StevenBlack/hosts/master/data/tiuxo/hosts -O hosts/hosts.4
wget -q https://raw.githubusercontent.com/StevenBlack/hosts/master/data/URLHaus/hosts -O hosts/hosts.5
wget -q https://raw.githubusercontent.com/StevenBlack/hosts/master/data/mvps.org/hosts -O hosts/hosts.6
sort hosts/hosts* | grep -Eo '^([^\\"'\''#]|\\.|"([^\\"]|\\.)*"|'\''[^'\'']*'\'')*' | uniq > enemy_hosts
rm -rf hosts
```
В результате был получен файл enemy_hosts.

4. Получим список посещенных доменов:

```{python}
with open("C:\\Users\\Asus\\Desktop\\dns.log") as f:
    lines_dns = f.readlines()
    dns_hosts = [line.split()[9] for line in lines_dns if len(line.split()) > 9]
f.close()
```

5. Получим список нежелательного трафика:

```{python}
with open("C:\\Users\\Asus\\Desktop\\enemy_hosts") as f:
    lines_enemy = f.readlines()
    enemy_hosts = [line.split()[1] for line in lines_enemy if len(line.split()) > 1]
f.close()
```

6. Получим количество совпадений DNS с нежелательным трафиком и процент нежелательного трафика:

```{python}
c=0
for i in range(len(dns_hosts)):
    for j in range(len(enemy_hosts)):
        if dns_hosts[i] == enemy_hosts[j]:
            c += 1
enemy_dns = c/(len(enemy_hosts))
print("Количество совпадений DNS с нежелательным трафиком:", c)
print("Процент нежелательного трафика:", enemy_dns)
```

## Оценка результата

В результате практической работы нежелательный трафик не был выявлен.

## Вывод

Таким образом, был освоен метод анализа сетевого трафика при помощи дополнительных инструментов.
