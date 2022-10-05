# 1. Основы LINQ

`FirstOrDefault: Возвращает первый элемент последовательности или значение по умолчанию, если последовательность не содержит элементов.`
`SingleOrDefault: Возвращает единственный элемент последовательности или значение по умолчанию, если последовательность пуста.`
`Этот метод создает исключение, если в последовательности более одного элемента.`

- язык запросов к данным
- в качестве данных может выступать объект, реализующий интерфейс IEnumerable
- вне зависимости какой это объект, LINQ позволяет применить ко всем один и тот же подход для выборки данных
- сокращает кода с помощью интуитивно понятного синтаксиса
- выражения LINQ - строготипизированы

Разновидности LINQ:

- to Object - массивы и коллекции
- to Entities - при обращении к БД через EF
- to XML
- to DataSet
- Parallel LINQ

## 1.1 Операторы запросов LINQ

- стандартный синтаксис LINQ - from .. in .. select

```csharp
var array = new string[] { "Emma", "Eduard", "Tom" };

var newArray1 = new string[3];

for (int i = 0; i < array.Length; i++)
{
    if (array[i].ToUpper().StartsWith("E"))
    {
        newArray1[i] = array[i];
    }
}

Print(newArray1);

var newArray2 = from item in array
                where item.ToUpper().StartsWith("E")
                select item;

Print(newArray2.ToArray());

void Print(string[] array)
{
    foreach (var item in array)
    {
        Console.WriteLine(item);
    }
}
```

## 1.2 Методы расширений LINQ

- методы рассширения в качестве аргумента принимают делегат и лямбда-выражение

```csharp
var array = new string[] { "Emma", "Eduard", "Tom" };

var result = array.Where(x => x.ToUpper().StartsWith("E"));

Print(result);

void Print(IEnumerable<string> list)
{
    foreach (var item in list)
    {
        Console.WriteLine(item);
    }
}
```

## 1.3 Общее

- можно сочетать две разновидности операторы запросов и методы расширения

```csharp
var array = new string[] { "Emma", "Eduard", "Tom" };

var count = (from item in array
             where item.ToUpper().StartsWith("E")
             select item).Count();

Console.WriteLine(count); // 2
```

# 2. Проекция данных

- позволяет преобразовать объект одного типа в другой
- использует оператор select

```csharp
// Получаем список имен

var list = new List<Person>()
{
    new Person("Eduard", 23),
    new Person("Emma", 21),
    new Person("Tom", 55)
};

var listNames = from item in list
                select item.name;
// ИЛИ
var listNames = list.Select(x => x.name);

foreach (var item in listNames)
{
    Console.WriteLine(item);
}
```

```csharp
var list = new List<Person>()
{
    new Person("Eduard", 23),
    new Person("Emma", 22),
    new Person("Tom", 55)
};

// можно создать список объектов анонимного типа

var result = from item in list
             select new
             {
                 Name = item.name,
                 Year = DateTime.Now.Year - item.age
             };
// ИЛИ
var result = list
    .Select(x => new { Name = x.name, Year = DateTime.Now.Year - x.age });

foreach (var item in result)
{
    Console.WriteLine($"Name: {item.Name}, Year: {item.Year}");
}
```

## 2.1 Переменые в запросах и оператор let

- если надо сделать какие-то дополнительные промежуточные вычисления
- оператор let
- в методах расширения такого нет

```csharp
var result = from item in list
             let name = $"Mr. {item.name}"
             let year = DateTime.Now.Year
             select new
             {
                 Name = name,
                 Year = year
             };
```

## 2.1 Выборка из нескольких источников

- каждый элемент из первого источника будет сопоставляться с каждый элементом из второго источника (4 пары, в текущем примере ниже)

```csharp
var courses = new List<Course>()
{
    new Course("C#"), new Course("Java")
};
var students = new List<Student>()
{
    new Student("Eduard"), new Student("Emma")
};

var result = from course in courses
             from student in students
             select new
             {
                 Student = student.Name,
                 Course = course.Title
             };

foreach (var item in result)
{
    Console.WriteLine($"Student: {item.Student}, Cource: {item.Course}");
}

record class Course(string Title);
record class Student(string Name);
```

## 2.2 SelectMany и сведение объектов

- метод, который сводит набор коллекций в одну

```csharp
var companies = new List<Company>()
{
    new Company ("Finteco",
        new List<Person>()
        {
            new Person("Eduard"),
            new Person("Emma")
        }),
    new Company ("Apple",
        new List<Person>()
        {
            new Person("Tom"),
            new Person("Bob")
        })
};

//var result = companies.SelectMany(x => x.Name); // IEnumerable<char>

//var result = companies.SelectMany(x => x.Staff); // IEnumerable<Person>

var result = companies.SelectMany(x => x.Staff, (c, p) => new { Company = c.Name, Person = p.Name }); // Анонимный объект

foreach (var item in result)
{
    Console.WriteLine($"Person: {item.Person}, Company: {item.Company}");
}

record class Company(string Name, List<Person> Staff);
record class Person(string Name);
```

В последнем примере:
- x => x.Staff - промежуточная коллекция (набор сотрудников)
- (с, р) => new ... - делегат, который получает каждую компанию и каждый элемент промежуточной коллекции, на основе их создает анонимный объект

## 3. Фильтрация коллекции

- выбор элемент из какого-то набора по условию
- метод Where, принимает делегат, который в качестве параметра принимает элемент последовательности и возвращает значение bool
- если элемент соответсвует некоторому условию, то возвращается true, и тогда этот элемент передается в коллекцию, которая возвращается с метода Where

```csharp
var array = new int[] { 1, 2, 3, 4, 1, 2 };

var result = array.Where(x => x == 1);

foreach (var item in result)
{
    Console.WriteLine(item);
}
```

## 3.1 Выборка сложных объектов

```csharp
var people = new List<Person>
{
    new Person ("Tom", 23, new List<string> {"english", "german"}),
    new Person ("Bob", 27, new List<string> {"english", "french" }),
    new Person ("Sam", 29, new List<string>  { "english", "spanish" }),
    new Person ("Alice", 24, new List<string> {"spanish", "german" })
};

var result = people.SelectMany(user => user.Languages, (u, l) => new {Person = u, Language = l})
    .Where(user => user.Language == "english");

foreach (var resultItem in result)
{
    Console.WriteLine(resultItem.Person);
}

record class Person(string Name, int Age, List<string> Languages);
```

# 3.2 Фильтрация по типу данных

- метод расширения OfType<>()

```csharp
var people = new List<Person>
{
    new Student("Tom"),
    new Person("Sam"),
    new Student("Bob"),
    new Employee("Mike")
};

var students = people.OfType<Student>();

foreach (var student in students)
    Console.WriteLine(student.Name);

record class Person(string Name);
record class Student(string Name) : Person(Name);
record class Employee(string Name) : Person(Name);
```

# 4. Сортировка

# 4.1 Оператор orderby и метод OrderBy

- первая версия делегат
- вторая версия делегат плюс реализацию интерфейса IComparerw

```csharp
var array = new int[] { 5, 2, 7, 2, 4, 6, 7 };

var result = from item in array
             orderby item
             select item;

foreach (var item in result)
{
    Console.WriteLine(item); // 2, 2, 4, 5, 6, 7, 7
}
```

```csharp
var array = new int[] { 5, 2, 7, 2, 4, 6, 7 };

foreach (var item in array.OrderBy(x => x))
{
    Console.WriteLine(item); // 2, 2, 4, 5, 6, 7, 7
}
```

```csharp
var array = new int[] { 5, 2, 7, 2, 4, 6, 7 };

foreach (var item in array.OrderByDescending(x => x))
{
    Console.WriteLine(item); // 7, 7, 6, 5, 4, 2, 2
}
```

# 4.2 Множественные критерии сортировки

```csharp
var sorted = people.OrderBy(p => p.Name).ThenByDescending(p=>p.Age);
```

# 5. Объединение, пересечение и разность коллекций

## 5.1 Разность последовательностей

```csharp
string[] soft = { "Microsoft", "Google", "Apple" };
string[] hard = { "Apple", "IBM", "Samsung" };

var result = soft.Except(hard); // Micrososft, Google

foreach (string s in result)
    Console.WriteLine(s);
```

## 5.2 Пересечение последовательностей

```csharp
string[] soft = { "Microsoft", "Google", "Apple" };
string[] hard = { "Apple", "IBM", "Samsung" };

var result = soft.Intersect(hard); // Apple

foreach (string s in result)
    Console.WriteLine(s);
```

## 5.3 Удаление дубликатов

```csharp
string[] soft = { "Microsoft", "Google", "Apple", "Microsoft" };

var result = soft.Distinct(); // "Microsoft", "Google", "Apple"

foreach (string s in result)
    Console.WriteLine(s);
```

## 5.4 Объединение последовательностей

- удаляет дубликаты

```csharp
string[] soft = { "Microsoft", "Google", "Apple" };
string[] hard = { "Apple", "IBM", "Samsung" };

var result = soft.Union(hard); // "Microsoft", "Google", "Apple", "IBM", "Samsung"

foreach (string s in result)
    Console.WriteLine(s);
```

## 5.5 Простое объединение двух наборов

- не удаляет дубликаты

```csharp
string[] soft = { "Microsoft", "Google", "Apple" };
string[] hard = { "Apple", "IBM", "Samsung" };

var result = soft.Concat(hard); // "Microsoft", "Google", "Apple", "Apple", "IBM", "Samsung"

foreach (string s in result)
    Console.WriteLine(s);
```

## 5.6 Работа со сложными объектами

- Для сравнения объектов в последовательностях применяются реализации методов GetHeshCode() и Equals(). Поэтому если мы хотим работать с последовательностями, которые содержат объекты своих классов и структур, то нам необходимо определить для них подобные методы

```csharp
Person[] students = { new Person("Tom"), new Person("Bob"), new Person("Sam") };
Person[] employees = { new Person("Tom"), new Person("Bob"), new Person("Mike") };

// объединение последовательностей
var people = students.Union(employees);

foreach (Person person in people)
    Console.WriteLine(person.Name); // Tom, Bob, Sam, Mike


class Person
{
    public string Name { get; }
    public Person(string name) => Name = name;

    public override bool Equals(object? obj)
    {
        if (obj is Person person) return Name == person.Name;
        return false;
    }

    public override int GetHashCode() => Name.GetHashCode();
}
```

# 6. Агрегатные операции

- 