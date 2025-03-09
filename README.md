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
## Создание коллекций
Создадим БД и подключимся к ней
```sh
use UniversityDB;
```
Скрипт для создания коллекций. В качестве альтернативы, скрипт можно сохранить в файл и запустить из файла
```sh
// Создание коллекции Students (Студенты)
db.createCollection("Students", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "surname", "group", "faculty", "year_of_admission"],
      properties: {
        name: { bsonType: "string", description: "Имя студента" },
        surname: { bsonType: "string", description: "Фамилия студента" },
        group: { bsonType: "string", description: "Группа студента" },
        faculty: { bsonType: "string", description: "Факультет" },
        year_of_admission: { bsonType: "int", description: "Год поступления" }
      }
    }
  }
});

// Создание коллекции Teachers (Преподаватели)
db.createCollection("Teachers", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "surname", "department", "position"],
      properties: {
        name: { bsonType: "string", description: "Имя преподавателя" },
        surname: { bsonType: "string", description: "Фамилия преподавателя" },
        department: { bsonType: "string", description: "Кафедра" },
        position: { bsonType: "string", description: "Должность" }
      }
    }
  }
});

// Создание коллекции Courses (Курсы)
db.createCollection("Courses", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["course_name", "teacher_id", "credits"],
      properties: {
        course_name: { bsonType: "string", description: "Название курса" },
        teacher_id: { bsonType: "objectId", description: "Идентификатор преподавателя" },
        credits: { bsonType: "int", description: "Количество кредитов" }
      }
    }
  }
});

// Создание коллекции Grades (Оценки)
db.createCollection("Grades", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["student_id", "course_id", "grade", "date"],
      properties: {
        student_id: { bsonType: "objectId", description: "Идентификатор студента" },
        course_id: { bsonType: "objectId", description: "Идентификатор курса" },
        grade: { bsonType: "int", minimum: 1, maximum: 5, description: "Оценка" },
        date: { bsonType: "date", description: "Дата выставления оценки" }
      }
    }
  }
});

// Создание коллекции Groups (Группы)
db.createCollection("Groups", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["group_name", "faculty", "year_of_admission"],
      properties: {
        group_name: { bsonType: "string", description: "Название группы" },
        faculty: { bsonType: "string", description: "Факультет" },
        year_of_admission: { bsonType: "int", description: "Год поступления" }
      }
    }
  }
});
```
Просмотреть список созданных коллекций
```sh
show collections;
```
В файле "Коллекции.png" представлен результат
## Индексы
Для ускорения поиска студентов по фамилии и имени
```sh
db.Students.createIndex({ surname: 1, name: 1 });
```
Для ускорения поиска курсов по названию
```sh
db.Courses.createIndex({ course_name: 1 });
```
Для ускорения поиска оценок по студенту и курсу
```sh
db.Grades.createIndex({ student_id: 1, course_id: 1 });
```
Созданные индексы в файлах "Индексы_1.png", "Индексы_2.png"

