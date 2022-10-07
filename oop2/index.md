# 1. Определение операторов

- операции арифметические, логические допустимы для примитивных типов, чтобы использовать для сложных, надо создать перегружаемый метод
- перегружаемый метод одного и того же оператора может иметь разные версии

```csharp
var counter1 = new Counter() { Value = 10 };
var counter2 = new Counter() { Value = 20 };

// Такие операции доступны для примитивных типов (которые определены компиляторов)
var result = counter1 > counter2;
var counter3 = counter1 + counter2;
var counter4 = counter1 + 10;

Console.WriteLine(result); // false
Console.WriteLine(counter3.Value); // 30
Console.WriteLine(counter4.Value); // 20

class Counter
{
    public int Value { get; set; }

    public static Counter operator +(Counter counter1, Counter counter2)
    {
        return new Counter() { Value = counter1.Value + counter2.Value };
    }

    public static Counter operator +(Counter counter, int value)
    {
        return new Counter() { Value = counter.Value + value };
    }

    public static bool operator >(Counter counter1, Counter counter2)
    {
        return counter1.Value > counter2.Value;
    }

    public static bool operator <(Counter counter1, Counter counter2)
    {
        return counter1.Value < counter2.Value;
    }
}
```

Логика для следующиъ операторов:

- унарные операторы +x, -x, !x, ~x, ++, --, true, false
- бинарные операторы +, -, \*, /, %
- операции сравнения (парами) ==, !=, <, >, <=, >=
- поразрядные операторы &, |, ^, <<, >>
- логические операторы &&, ||

Есть ряд операторов, которые нельзя перегрузить, например, операцию равенства = или тернарный оператор ?:, а также ряд других.

- Следует учитывать, что в коде оператора не должны изменяться те объекты, которые передаются в оператор через параметры

# 2. Перегрузка операций преобразования типов

- то есть преобразования примитивного типа к сложному, или наоборот

```csharp
var counter = new Counter() { Seconds = 20 };
var seconds = (int)counter;

Console.WriteLine(counter.Seconds); // 20
Console.WriteLine(seconds); // 20

class Counter
{
    public int Seconds { get; set; }

    public static implicit operator Counter(int x)
    {
        return new Counter { Seconds = x };
    }

    public static explicit operator int(Counter counter)
    {
        return counter.Seconds;
    }
}
```

# 3. Индексаторы (перечитать)

- позволяют индексировать объекты и обращаться к данным по индексу
- то есть можем работать с объектами как с массивами
- по форме напоминают свойства с блоками get & set

```csharp
var company = new Company(new Person[]
{
    new Person("Eduard"),
    new Person("Emma")
});

// Но как к ним обратиться? Надо создать индексатор в классе Company

Console.WriteLine(company[0].Name);
Console.WriteLine(company[1].Name);

class Person
{
    public string Name { get; set; }

    public Person(string name)
    {
        Name = name;
    }
}

class Company
{
    private Person[] _people;

    public Company(Person[] people)
    {
        _people = people;
    }

    public Person this[int index]
    {
        get => _people[index];
        set => _people[index] = value;
    }
}
```

## 3.1 Индексы

# 4. Переменные-ссылки и возвращение ссылки

# 4.1 Переменная-ссылка

- переменная xRef указывает не на значение, а на область в памяти, где располагается эта переменная
- нельзя объявить ссылочную переменную без значения

```csharp
int x = 5;

ref int xRefTest1; // error
ref int xRefTest2 = x; // error
ref int xRef = ref x;
```

```csharp
int x = 5;

ref int xRef = ref x;
xRef = 3;

Console.WriteLine(x); // 3
Console.WriteLine(xRef); // 3
```

## 4.2 Ссылка как результат функции

Пример без ссылки

```csharp
var numbers = new int[] { 1, 2, 3, 4, 5 };
var result = Find(numbers, 3);

result = 5;

Console.WriteLine(numbers[2]); // 3

int Find(int[] numbers, int value)
{
    for (int i = 0; i < numbers.Length; i++)
    {
        if (numbers[i] == value)
        {
            return numbers[i];
        }
    }

    return 0;
}
```

Пример с ссылкой

```csharp
var numbers = new int[] { 1, 2, 3, 4, 5 };
ref int result = ref Find(numbers, 3);

result = 5;

Console.WriteLine(numbers[2]); // 5

ref int Find(int[] numbers, int value)
{
    for (int i = 0; i < numbers.Length; i++)
    {
        if (numbers[i] == value)
        {
            return ref numbers[i];
        }
    }

    throw new Exception("Value not found");
}
```

# 5. Методы расширения

- позволяют добавлять новые методы в уже существующие типы, без создания производного класса
- метод расширения никогда не будет вызван, если он имеет ту же сигнатуру, что и метод, изначально определенный в типе
- методы расширения действуют на уровне пространства имен

Шаги:
1. создать статический класс
2. создать статический метод
3. первый параметр статического метода с ключевым словом this

```csharp
var str = "hello";
var ch = 'l';

Console.WriteLine(str.CharCount(ch));

static class StringExtension
{
    public static int CharCount(this string str, char c)
    {
        int counter = 0;

        for(int i = 0; i < str.Length; i++)
        {
            if(str[i] == c)
            {
                counter++;
            }
        }
        
        return counter;
    }
}
```

# 6. Частичные классы и методы

- можно иметь несколько файлов с определением одного и того же класса, и при компиляции все эти определения будут скомпилированы в одно

Создать два разных файла

```csharp
using ConsoleApp;

var test = new Base();
test.Move();
test.Eat();
///////////////////////
namespace ConsoleApp
{
    internal partial class Base
    {
        public void Move()
        {
            Console.WriteLine("I'm moving");
        }
    }
}
///////////////////////
namespace ConsoleApp
{
    internal partial class Base
    {
        public void Eat()
        {
            Console.WriteLine("I'm eating");
        }
    }
}
```

## 6.1 Частичные методы

- частичные классы, могут содержать частичные методы

Ограничения (под вопросом): 
- Они не могут иметь модификаторы доступа
- Они имеют тип void
- Они не могут иметь out-параметры
- Они не могут иметь модификаторы virtual, override, sealed, new или extern

```csharp
namespace ConsoleApp
{
    public partial class Base
    {
        private partial void Move();
        
        public void DoSomething()
        {
            Move();
        }
    }

    public partial class Base
    {
        private partial void Move()
        {
            Console.WriteLine("I'm moving");
        }
    }
}
```

# 7. Анонимные типы

- позволяют создать объект с набором свойств без определения класса
- свойства анонимного типа доступны только для чтения
- на этапе компиляции, компилятор сам определяет тип для анонима (пример: AnonymousType)
- для CLR - это ссылочный тип
- если в программе создаются два анонимных типа с одинаковым набором свойств, то компилятор для них создаст одно определение типа
- значения можно установить только в инициализаторе

```csharp
var pupil = new { Name = "Eduard", Age = 23 };
var student = new { Name = "Eduard", Age = 23 };
var developer = new { Name = "Emma", Age = 23, Company = "Finteco" };

/// <summary>
/// <>f__AnonymousType0`2
/// <> f__AnonymousType0`2
/// <> f__AnonymousType1`3
/// </summary>

Console.WriteLine(pupil.GetType().Name);
Console.WriteLine(student.GetType().Name);
Console.WriteLine(developer.GetType().Name);
```

# 8. Кортежи

- способ для работы со значениями
- кортеж может выступать как результат или параметр метода

```csharp
var tuple = (1, 2);
Console.WriteLine(tuple); // (1, 2)
Console.WriteLine(tuple.Item1); // 1
Console.WriteLine(tuple.Item2); // 2

(int, string) tuple2 = (23, "Eduard");
(int, string) tuple3 = (Age: 23, Name: "Eduard");

Console.WriteLine(tuple2);
Console.WriteLine(tuple3);
```

- можно применить для обмена значениями

```csharp
var pupil = "Eduard";
var student = "Emma";

(pupil, student) = (student, pupil);

Console.WriteLine(pupil + " " + student); // Emma Eduard
```

# 9. Records

- ссылочный тип
- неизменяемый тип, но при определенных условиях

Какие условия для неизменяемого типа:
- блок init в свойстве

```csharp
public record Person1
{
    public string Name { get; set; }

    public Person1(string name)
    {
        Name = name;
    }
}

public record class Person2 { }
public record struct Person3 { }
```

Отличия от классов и структур:
- сравнение на равенство (на основе значений объекта)
- оператор with (как и у структур, позволяет создать один record на основе другого)
- сокращенная запись объявления record
- реализован метод ToString()

Для сокращенной записи, создается:
- свойства
- конструктор
- деконструктор

```csharp
var person = new Person("Eduard");
Console.WriteLine(person.Name);

public record Person(string Name);
```

Сокращенные структуры в сравнении с классами:
- свойства будут иметь блоки { get; set; }, а не { get; init; }
- чтобы нельзя было менять значения сделать readonly структуру