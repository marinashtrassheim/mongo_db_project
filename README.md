# mongo_db_project
Здесь описано тестовый проект базы данных для учёта оценок студентов университета
## Схема БД
Коллекции и их связь представлены в файле "Схема БД.png"
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
## Наполнение коллекций тестовыми данными
Вставка данных в коллекцию Students
```sh
db.Students.insertMany([
  { name: "Иван", surname: "Иванов", group: "ГР-101", faculty: "Информатика", year_of_admission: 2020 },
  { name: "Мария", surname: "Петрова", group: "ГР-102", faculty: "Математика", year_of_admission: 2021 },
  { name: "Алексей", surname: "Сидоров", group: "ГР-101", faculty: "Информатика", year_of_admission: 2020 }
]);
```
Вставка данных в коллекцию Teachers
```sh
db.Teachers.insertMany([
  { name: "Анна", surname: "Смирнова", department: "Кафедра информатики", position: "Доцент" },
  { name: "Дмитрий", surname: "Козлов", department: "Кафедра математики", position: "Профессор" },
  { name: "Ольга", surname: "Васильева", department: "Кафедра физики", position: "Ассистент" }
]);
```
Вставка данных в коллекцию Courses
```sh
db.Courses.insertMany([
  { course_name: "Базы данных", teacher_id: db.Teachers.findOne({ surname: "Смирнова" })._id, credits: 4 },
  { course_name: "Математический анализ", teacher_id: db.Teachers.findOne({ surname: "Козлов" })._id, credits: 5 },
  { course_name: "Физика", teacher_id: db.Teachers.findOne({ surname: "Васильева" })._id, credits: 3 }
]);
```
Вставка данных в коллекцию Groups
```sh
db.Groups.insertMany([
  { group_name: "ГР-101", faculty: "Информатика", year_of_admission: 2020 },
  { group_name: "ГР-102", faculty: "Математика", year_of_admission: 2021 }
]);
```
Вставка данных в коллекцию Grades
```sh
const student1 = db.Students.findOne({ surname: "Иванов" })._id;
const student2 = db.Students.findOne({ surname: "Петрова" })._id;
const course1 = db.Courses.findOne({ course_name: "Базы данных" })._id;
const course2 = db.Courses.findOne({ course_name: "Математический анализ" })._id;

db.Grades.insertMany([
  { student_id: student1, course_id: course1, grade: 5, date: new Date("2023-10-01") },
  { student_id: student1, course_id: course2, grade: 4, date: new Date("2023-10-02") },
  { student_id: student2, course_id: course1, grade: 3, date: new Date("2023-10-03") },
  { student_id: student2, course_id: course2, grade: 5, date: new Date("2023-10-04") }
]);
```
## Запросы
Получение списка всех студентов -> "Запрос_1.png"
```sh
db.Students.find({});
```
Получение списка всех курсов, которые ведёт определённый преподаватель -> "Запрос_2.png"
```sh
db.Courses.find({ teacher_id: ObjectId("67cd6da3fbaa1f6974d08e1b") });
```
Получить список всех студентов определённой группы -> "Запрос_3.png"
```sh
db.Courses.find({ teacher_id: ObjectId("67cd6da3fbaa1f6974d08e1b") });
```
Получить список всех оценок определённого студента -> "Запрос_4.png"
