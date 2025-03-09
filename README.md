# mongo_db_project
Здесь описано тестовый проект базы данных для учёта оценок студентов университета
## Схема БД
Коллекции и их связь представлены в файле "Схема БД.png"
## Установка mongo на mac os
Установить базу данных
```sh
brew tap mongodb/brew
```
```sh
brew install mongodb-community
```
Запустить базу данных
```sh
brew services start mongodb/brew/mongodb-community
```
```sh
mongosh
```
В результате в терминале должна открыться консоль для работы с базой данных
