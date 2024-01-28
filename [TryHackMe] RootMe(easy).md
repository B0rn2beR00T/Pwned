# Разведка

~~~
sudo nmap -A 10.10.61.18
~~~

![Pasted image 20240121180724](https://github.com/Cooper14052/Pwned/assets/79376063/5c23cf98-4601-4d52-9d99-283c5d2bd19e)

Видим 2 открытых порта - 22 и 80

![Pasted image 20240121180845](https://github.com/Cooper14052/Pwned/assets/79376063/b1968d37-4add-47a8-a89e-6629ca5e951f)
![Pasted image 20240121180926](https://github.com/Cooper14052/Pwned/assets/79376063/832eef67-7f31-4d85-85df-948663f812c0)
![Pasted image 20240121180945](https://github.com/Cooper14052/Pwned/assets/79376063/f441abce-46cc-4fae-bba6-38012eb428b3)

Заходим на веб-страницу машины
![Pasted image 20240121181448](https://github.com/Cooper14052/Pwned/assets/79376063/fcc4f805-6ebf-482d-a395-d87b174192b7)
Сканируем директории 
~~~
gobuster dir -u http://10.10.61.18/ -w /usr/share/wordlists/dirb/common.txt
~~~
![Pasted image 20240121181731](https://github.com/Cooper14052/Pwned/assets/79376063/39ddef6c-9310-4eb5-83d2-9f702427cbb5)
Находим 2 интересующие нас директории - /panel и /uploads с 301 кодом HTTP(перенаправление). Если зайти в директорию js, то можно проинспектировать безопасность кода страницы.
![Pasted image 20240121181803](https://github.com/Cooper14052/Pwned/assets/79376063/45307a7b-77f5-4df0-98c2-36537f2754c7)
Просматриваем директорию /panel и наблюдаем страницу с возможностью загружать различные файлы
![Pasted image 20240121181910](https://github.com/Cooper14052/Pwned/assets/79376063/24f2f85c-0c38-475b-88b9-3094bc897c81)
# Внедрение в систему

Попробуем использовать популярный ревёрс-shell от pentestmonkey
![Pasted image 20240121182158](https://github.com/Cooper14052/Pwned/assets/79376063/b75422de-8159-41a1-b306-e931b210d1d1)
Изменяем параметр ip в коде шелла на свой(vpn)
![Pasted image 20240121183054](https://github.com/Cooper14052/Pwned/assets/79376063/e434fc90-edaa-49e1-bbcc-e3b1019eabd4)
И наблюдаем ошибку...
![Pasted image 20240121184139](https://github.com/Cooper14052/Pwned/assets/79376063/61521bfd-5353-4980-9196-0e0d3f1961bc)
Пробуем обмануть систему защиты от php кода и изменяем формат шелла на php5
![Pasted image 20240121184056](https://github.com/Cooper14052/Pwned/assets/79376063/8bd08e11-2483-433f-ba33-e5c225ff10ab)
И такой трюк срабатывает
Используем утилиту netcat для получения подключения
~~~
nc -lvnp 1234
~~~
Заходим в директорию uploads и получаем подключение к машине
![Pasted image 20240121184447](https://github.com/Cooper14052/Pwned/assets/79376063/da9f92e8-9619-4353-951c-57a1fe6cc2d3)
![Pasted image 20240121184610](https://github.com/Cooper14052/Pwned/assets/79376063/d85695e2-2b1d-4268-a6a0-fed73fe322f9)
Ищем пользовательский флаг
~~~
find / -name user.txt
~~~
![Pasted image 20240121232901](https://github.com/Cooper14052/Pwned/assets/79376063/d6374185-44e0-4e4c-b985-bcc18ca94999)
![Pasted image 20240121232957](https://github.com/Cooper14052/Pwned/assets/79376063/fab91918-7683-49bd-8423-c421369a3f6d)
И получаем желанный результат
![Pasted image 20240121233034](https://github.com/Cooper14052/Pwned/assets/79376063/e44cba32-b08f-4a5e-8e5b-e6a3d8a12174)
# Повышение привелегий

В самом задании мы имеем подсказку о SUID файлах
Производим поиск всех файлов с SUID
~~~
 find / -type f -user root -perm -u=s 2> /dev/null  
~~~
![Pasted image 20240121233857](https://github.com/Cooper14052/Pwned/assets/79376063/502f5d4c-a7f4-4389-ab82-39787aee895d)
Нас интересует python
![Pasted image 20240121234345](https://github.com/Cooper14052/Pwned/assets/79376063/a3f2bdca-5ab8-437e-b8ca-84ee62c5ce87)
Воспользуемся специальным поисковиком для повышения привилегий 
![Pasted image 20240121233943](https://github.com/Cooper14052/Pwned/assets/79376063/8c9100fc-2a14-4e50-aa49-355dc3104234)
Находим нужную команду
![Pasted image 20240121234026](https://github.com/Cooper14052/Pwned/assets/79376063/2e974c32-1aff-475f-9e8e-e794d61c2473)
И получаем root флаг
![Pasted image 20240121234200](https://github.com/Cooper14052/Pwned/assets/79376063/04cc8d9c-2b0a-4522-9b37-4dd876d022da)
![Pasted image 20240121234248](https://github.com/Cooper14052/Pwned/assets/79376063/3f9bfec1-a43a-47a0-a8e8-77c2010956e7)
![Pasted image 20240121234427](https://github.com/Cooper14052/Pwned/assets/79376063/216dec1b-1806-4e5b-a7ff-e4a9b1526582)
В дальнейшем я хочу найти больше способов пройти эту машину. На данный момент публикую в таком виде, но буду вносить поправки с найденными уязвимостями.
