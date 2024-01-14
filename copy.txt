import sqlite3
import os
from sqlite3 import Error
import json
# БУДЕМ ИСПОЛЬЗОВАТЬ sqlite3 ЧТОБЫ СДЕЛАТЬ БАЗУ ДАННЫХ И ОБРАЩАТЬСЯ ЗАПРОСАМИ
#задание 29


database ="test2.db"
con = sqlite3.connect("test2.db")
#print(con)
print(database)
cursor = con.cursor()

first_sql_table=cursor.execute("""CREATE TABLE IF NOT EXISTS flight_schedule  
                  (company TEXT,    
                   fl_number INT,   
                    departure TEXT, 
                    arrival TEXT,   
                    dep_time INT,  
                    arr_time INT,  
                    transfer TEXT,  
                   tariffs_id INT );    
                  """)
# таблица с тарифами
second_sql_table=cursor.execute("""CREATE TABLE IF NOT EXISTS tariffs  
                                (tariffs_id INT ,
                                 class_name TEXT,
                                 price INT
                                );     """)

third_sql_table=cursor.execute("""CREATE TABLE IF NOT EXISTS cities  
                                    (city_id INT,                    
                                     city TEXT);                                                                                      
                                """)
# вроде бы создали таблицы

#теперь вставляем данные в таблицу


# cities = [('Lufthansa', '130', 'Berlin', 'Moscow', '2020-04-24 14:54:00.000', '2020-04-24 20:04:00.0', 'Kiev', '1'),
#           ('TransAvia', '200', 'Minsk', 'Madrid', '2020-04-24 3:45:00.000', '2020-04-24 9:04:00.0', 'None', '3'),
#           ('Liberte', '204', 'London', 'Kairo', '2020-04-24 6:28:00.000', '2020-04-24 12:34:00.0', 'None', '3'),
#           ('Amsetrdamavia', '241', 'Berlin', 'Pert', '2020-04-24 7:01:00.000', '2020-04-24 19:27:00.0', 'Singapore', '3'),
#           ('FlyEmirates', '1041', 'Los Angeles', 'Toronto', '2020-04-24 11:24:00.000', '2020-04-24 18:44:00.0', 'None', '1')]
#
# #cursor.executemany("INSERT INTO flight_schedule VALUES (?,?,?,?,?,?,?,?);", cities)
tariffs=[
    cursor.execute("INSERT INTO tariffs VALUES('1','Эконом только', '10000')"),
    cursor.execute("INSERT INTO tariffs VALUES('2','Бизнес есть', '25000')")
]
con.commit()

flights=[
cursor.execute("INSERT INTO flight_schedule VALUES('Lufthansa', '1', 'Berlin', 'Moscow', '2020-04-24 14:54:00.000', '2020-04-24 20:04:00.000', 'Kiev', '1')"),
cursor.execute("INSERT INTO flight_schedule VALUES('TransAvia', '2', 'Minsk', 'Madrid', '2020-04-24 03:45:00.000', '2020-04-24 09:04:00.000', 'None', '2')"),
cursor.execute("INSERT INTO flight_schedule VALUES('Liberte', '3', 'London', 'Kairo', '2020-04-24 06:28:00.000', '2020-04-24 12:34:00.000', 'None', '2')"),
cursor.execute("INSERT INTO flight_schedule VALUES('Amsetrdamavia', '4', 'Berlin', 'Pert', '2020-04-24 07:01:00.000', '2020-04-24 19:27:00.000', 'Singapore', '1')"),
cursor.execute("INSERT INTO flight_schedule VALUES('FlyEmirates', '5', 'Los Angeles', 'Toronto', '2020-04-24 11:24:00.000', '2020-04-24 18:44:00.000', 'None', '1')"),
cursor.execute("INSERT INTO flight_schedule VALUES('jarjar', '6', 'Nizhnevartovsk', 'Samara', '2020-04-24 05:01:00.000', '2020-04-24 15:27:00.000', 'Moscow', '1')"),
cursor.execute("INSERT INTO flight_schedule VALUES('ZB', '7', 'Beijing', 'Nante', '2020-04-24 04:01:00.000', '2020-04-24 12:21:00.000', 'Moscow', '2')"),
cursor.execute("INSERT INTO flight_schedule VALUES('Sub_Doicth_LuftHansa', '1084', 'Berlin', 'Moscow', '2020-04-24 10:27:00.000', '2020-04-24 12:03:00.000', 'None', '1')")
]
#сохраняем изменения
con.commit()
#сделать норм вывод в консоль сюда
#сделать айди номера каблучки все дела
#прописать селекты
def execute_read_query(query):
       # cursor = connection.cursor()
        result = None
        try:
            cursor.execute(query)
            result = cursor.fetchall()
            return result
        except Error as e:
            print(f"The error '{e}' occured")
#####
select_showing= "SELECT * FROM flight_schedule"
flights = execute_read_query(select_showing)

for flight in flights:
    print(flight)
#####
try:

    print("Первый запрос где минимальное время полета, сначала все интервалы в часах:")
    select_first= "SELECT (julianday(arr_time)-julianday(dep_time))* 24  As Integer FROM flight_schedule "
    first = execute_read_query(select_first)

    for first in first:
        print(first)

    print("А теперь нужный (минимальный в часах):")
    select_first= "SELECT MIN((julianday(arr_time)-julianday(dep_time))*24 ) FROM flight_schedule "
    first = execute_read_query(select_first)
    print(first)
    #####
    print("Второй запрос где один город с пересадкой (Москва):")
    select_second = "SELECT * FROM flight_schedule WHERE transfer= 'Moscow'"
    second = execute_read_query(select_second)

    for second in second:
        print(second)

    select_second="SELECT COUNT(*) FROM flight_schedule WHERE transfer= 'Moscow'"
    second= execute_read_query(select_second)

    print("Вот число этих рейсов", second)

    #####
    print("Третий запрос- выводим компании где есть бизнес класс")
    select_third = "SELECT flight_schedule.company,flight_schedule.fl_number,tariffs.class_name,tariffs.price FROM flight_schedule INNER JOIN tariffs ON flight_schedule.tariffs_id=2 AND tariffs.tariffs_id=2"
    third= execute_read_query(select_third)

    for third in third:
        print(third)

    #####
    print("Четвертый запрос")
    print("Сейчас выведутся номера рейсов, которые прибывают ранее 14 часов")
    #time= (input())
    #late= '2020-04-24 '+time
    #late= late+":00:00"
    select_fourth = "SELECT fl_number FROM flight_schedule WHERE DateTime(arr_time) <= DateTime('2020-04-24 14:00:00.000')"
    fourth= execute_read_query(select_fourth)

    for fourth in fourth:
        print(fourth)

    #####
    #####
    print("Пятый запрос список рейсов, летающих по заданному маршруту (Берлин-Москва)")
    select_fifth = "SELECT * FROM flight_schedule WHERE departure='Berlin' AND arrival='Moscow'"
    fifth= execute_read_query(select_fifth)

    for fifth in fifth:
        print(fifth)
    #####

    def delete_table(table_name):
    # функция для удаления таблицы из базы данных
        query = 'DROP TABLE IF EXISTS '+table_name
        cursor.execute(query)
        print("Table is deleted ", table_name)

    def delete_database():
    #удаляем базу данных
        #закрываем курсор
        cursor.close()
        #закрываем соед. с базой
        con.close()
        #удаляем файл
        os.remove("test2.db")
        print("Database is deleted", database)
        print("Всё очистилось")

    def last_menu():
        j=0
        print("Введите число 1, если хотите удалить все таблицы вообще очистить базу данных, 2- если нет")
        j=int(input())

        if j==1:
            delete_table('flight_schedule')
            delete_table('tariffs')
            delete_table('cities')
            delete_database()
            return
        return
finally:
    last_menu()