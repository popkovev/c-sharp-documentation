# 1. Определение интерфейсов

- ссылочный тип
- контракт, что какой-то тип обязательно реализует некоторый функционал
- определяют функционал (методы и свойства без реализации), затем этот функционал реализуют классы или структуры

Что может определять интерфейс:

- методы
- свойства
- индексаторы
- события
- статические поля и константы
- не статические переменные - НЕЛЬЗЯ!!!

```csharp
interface IMovable
{
    const int minSpeed = 0;
    static int maxSpeed = 60;

    void Move();

    string Name { get; set; }

    delegate void MoveHandler(string message);
    event MoveHandler MoveEvent;
}
```

## 1.1 Модификаторы доступа

- интерфейс - по умолчанию internal
- члены по умолчанию имеют модификатор public
- c приходом 8 версии, можно задать для членов интерфейса модификатор

```csharp
Console.WriteLine(IMovable.minSpeed);
Console.WriteLine(IMovable.maxSpeed);
```

```csharp
interface IMovable
{
    public const int minSpeed = 0;
    private static int maxSpeed = 60;

    protected internal void Move();

    string Name { get; set; }

    delegate void MoveHandler(string message);
    event MoveHandler MoveEvent;
}
```

## 1.2 Реализация по умолчанию

- c приходом 8 версии можно сделать реализацию методов и свойств по умолчанию
- если объявить методы и свойства с модификатор private - необходимо сделать реализацию по умолчанию

```csharp
interface IMovable
{
    private static int _maxSpeed;

    static int MaxSpeed
    {
        get => _maxSpeed;
        set => _maxSpeed = value;
    }

    void Move() => Console.WriteLine("Move");
}
```

# 2. Применение интерфейсов

- нельзя создвать объекты интерфейса, с помощью конструктора, как в классах

```csharp
var move = new IMovable(); // error

interface IMovable
{
    void Move();
}
```

```csharp
var person = new Person();
var car = new Car();

DoAction(person);
DoAction(car);

void DoAction(IMovable movable) => movable.Move();

interface IMovable
{
    void Move();
}

class Person : IMovable
{
    public void Move() => Console.WriteLine("Person - move");
}

struct Car : IMovable
{
    public void Move() => Console.WriteLine("Car - move");
}
```

## 2.1 Реализация интерфейсов по умолчанию

- есть множество классов, реализующих какой-то интерфейс, если добавить новый метод в интерфейс, то придется реализовывать новый метод в каждом классе

```csharp
var person = new Person();
var car = new Car();

DoAction(person);
DoAction(car);

void DoAction(IMovable movable) => movable.Move();

interface IMovable
{
    void Move()
    {
        Console.WriteLine("Interface - Move");
    }
}

class Person : IMovable { }

struct Car : IMovable
{
    public void Move() => Console.WriteLine("Car - move");
}
```

```csharp
var person = new Person();
person.Move(); // error

DoAction(person); // correct
void DoAction(IMovable movable) => movable.Move();

interface IMovable
{
    void Move()
    {
        Console.WriteLine("Interface - Move");
    }
}

class Person : IMovable { }
```

## 2.2 Множественная реализация интерфейсов

```csharp
var editor = new Editor() { Text = "Hello world!!!" };
editor.Print();

interface IMessage
{
    string Text { get; set; }
}

interface IPrintable
{
    void Print();
}

class Editor : IMessage, IPrintable
{
    public string Text { get; set; }

    public void Print() => Console.WriteLine(Text);
}
```

## 2.3 Интерфейсы в преобразованиях типов

```csharp
IMessage message = new Editor() { Text = "Hello!!!" };
// - message.Print(); // error
Console.WriteLine(message.Text);

// - Editor editor = message; // error

if (message is Editor editor) editor.Print();
if (message is IPrintable printable) printable.Print();

interface IMessage
{
    string Text { get; set; }
}

interface IPrintable
{
    void Print();
}

class Editor : IMessage, IPrintable
{
    public string Text { get; set; }

    public void Print() => Console.WriteLine(Text);
}
```

# 3. Явная реализация интерфейсов

- это когда указывается название метода или свойства вместе с названием интерфейса

```csharp
var action1 = new BaseAction();
// - action1.Move(); // error

BaseAction action2 = new BaseAction();
// - action2.Move(); // error

IAction action3 = new BaseAction();
action3.Move(); // BaseAction - Move

// Безопасное приведение
if (action1 is IAction action) action.Move(); // BaseAction - Move

// Небезопасное приведение
((IAction)action1).Move(); // BaseAction - Move

interface IAction
{
    void Move();
}

class BaseAction : IAction
{
    void IAction.Move() => Console.WriteLine("BaseAction - Move");
}
```

- явная реализация требуется тогда, когда класс реализует набор интерфейсов с одинаковыми членами и при этом надо для каждого из них сделать отдельную реализацию
- когда в базовом классе уже реализован метод, а в производном надо сделать другую реализацию интерфейса
- т.к. члены интерфейса могут иметь модификаторы, то если он не public, значит надо использовать явную реализацию

```csharp
var person = new Person();

if (person is ISchool school) school.Study();
if (person is IUniversity university) university.Study();

interface ISchool
{
    void Study();
}

interface IUniversity
{
    void Study();
}

class Person : ISchool, IUniversity
{
    void ISchool.Study() => Console.WriteLine("School - study");

    void IUniversity.Study() => Console.WriteLine("University - study");
}
```

# 4. Реализация интерфейсов в базовых и производных классах

- если в классе нет надобности реализовывать какой-то член интерфейса, то его можно пометить как абстрактный
- если метод реализован в базовом классе, то в производном его можно не реализовывать

```csharp
interface IAction
{
    void DoAction();
}

abstract class Person : IAction
{
    public abstract void DoAction();
}
```

```csharp
interface IAction
{
    void DoAction();
}

class BaseAction : IAction
{
    public void DoAction() => Console.WriteLine("BaseAction - DoAction");
}

class HeroAction : BaseAction, IAction
{

}
```

# 4.1 Изменение реализации интерфейсов в производных классах

Если базовый класс реализовал интерфейс, а в производном классе нам надо изменить реализацию, то мы можем:

- воспользоваться либо переопределением
- либо скрытием
- либо сделать повторную реализацию интерфейса в производном классе
- либо явная реализация интерфейса

```csharp
interface IAction
{
    void Move();
}
class BaseAction : IAction
{
    public virtual void Move() => Console.WriteLine("Move in BaseAction");
}
class HeroAction : BaseAction
{
    public override void Move() => Console.WriteLine("Move in HeroAction");
}
```

```csharp
interface IAction
{
    void Move();
}
class BaseAction : IAction
{
    public void Move() => Console.WriteLine("Move in BaseAction");

}
class HeroAction : BaseAction
{
    public new void Move() => Console.WriteLine("Move in HeroAction");
}
```

```csharp
interface IAction
{
    void Move();
}
class BaseAction : IAction
{
    public void Move() => Console.WriteLine("Move in BaseAction");
}
class HeroAction : BaseAction, IAction
{
    public new void Move() =>  Console.WriteLine("Move in HeroAction");
}
```

```csharp
interface IAction
{
    void Move();
}
class BaseAction : IAction
{
    public void Move() => Console.WriteLine("Move in BaseAction");
}
class HeroAction : BaseAction, IAction
{
    public new void Move() => Console.WriteLine("Move in HeroAction");
    // явная реализация интерфейса
    void IAction.Move() => Console.WriteLine("Move in IAction");
}
```

# 5. Наследование интерфейсов

- интерфейсы могут наследоваться друг от друга
- нельзя применять модификатор selead, abstract
- методы интерфейсов могут использовать ключевое слово new для скрытия методов из базового интерфеса
- при наследовании должен учитываться модификатор доступа, такой же или более строгий

```csharp
interface IAction
{
    void DoAction();
}

interface IMoveAction : IAction
{
    int MaxSpeed { get; set; }
}

class BaseAction : IMoveAction
{
    private int _maxSpeed;

    public int MaxSpeed
    {
        get => _maxSpeed;
        set => _maxSpeed = value < 0 ? 0 : value;
    }

    public void DoAction()
    {
        Console.WriteLine($"MaxSpeed: {_maxSpeed}");
    }
}
```

```csharp
IAction action = new BaseAction();
action.Move(); // IAction

IRunAction runAction = new BaseAction();
runAction.Move(); // IRunAction

interface IAction
{
    void Move() => Console.WriteLine("IAction");
}

interface IRunAction : IAction
{
    new void Move() => Console.WriteLine("IRunAction");
}

class BaseAction : IRunAction { }
```

# 6. Интерфейсы как ограничения обобщений

- интерфейсы могут выступать в качестве ограничений обобщений
- в качестве ограничения можно указать один класс, то интерфейсов несколько

```csharp
var telegram = new Messenger<Message>();
var message1 = new Message("Hello World!!!");

telegram.Send(message1);

var aska = new Messenger<Message2>(); // error - не реализует IMessage

interface IMessage
{
    string Text { get; set; }
}

interface IPrintable
{
    void Print();
}

class Message : IMessage, IPrintable
{
    public string Text { get; set; }

    public Message(string text)
    {
        Text = text;
    }

    public void Print() => Console.WriteLine(Text);
}

class Message2 : IPrintable
{
    public void Print() => Console.WriteLine("Message2");
}

class Messenger<T> where T : IMessage, IPrintable
{
    public void Send(T message)
    {
        message.Print();
    }
}
```

## 6.1 Обобщенные интерфейсы

- интефейсы могут быть обобщенными

```csharp
var user = new User<string>() { Id = "123-125-123" };

interface IUser<T>
{
    T Id { get; set; }
}

class User<T> : IUser<T>
{
    public T Id { get; set; }
}
```

```csharp
var user = new User() { Id = "123-125-123" };

interface IUser<T>
{
    T Id { get; set; }
}

class User : IUser<string>
{
    public string Id { get; set; }
}
```

# 7. Копирование объектов. Интерфейс ICloneable

- т.к. классы ссылочные типы, есть ограничение на их использование. При копировании, они объекты указывают на один и тот же участок в памяти

```csharp
var person1 = new Person(1, "Eduard");
var person2 = person1;
person2.Name = "Emma";

Console.WriteLine(person1.Name); // Emma

class Person
{
    public int Id { get; set; }
    public string Name { get; set; }

    public Person(int id, string name)
    {
        Id = id;
        Name = name;
    }
}
```

## 7.1 Поверхностное копирование

- копирование происходит с реализацие интерфейса IClonable
- для сокращения кода копирования мы можем использовать специальный метод MemberwiseClone(), который возвращает копию объекта
- поверхностное копирование - копируюет примитивные типы

```csharp
var person1 = new Person(1, "Eduard");
var person2 = person1.Clone() as Person;
person2.Name = "Emma";

Console.WriteLine(person1.Name); // Eduard

class Person : ICloneable
{
    public int Id { get; set; }
    public string Name { get; set; }

    public Person(int id, string name)
    {
        Id = id;
        Name = name;
    }

    public object Clone() => new Person(Id, Name);
    // public object Clone() => MemberwiseClone();
}
```

```csharp
var company = new Company("Finteco");
var person1 = new Person(1, "Eduard", company);
var person2 = person1.Clone() as Person;
person2.Company.Name = "Apple";

Console.WriteLine(person1.Company.Name); // Apple

class Person : ICloneable
{
    public int Id { get; set; }
    public string Name { get; set; }
    public Company Company { get; set; }

    public Person(int id, string name, Company company)
    {
        Id = id;
        Name = name;
        Company = company;
    }

    public object Clone() => MemberwiseClone();
}

class Company
{
    public string Name { get; set; }

    public Company(string name)
    {
        Name = name;
    }
}
```

## 7.2 Глубокое копирование

```csharp
var company = new Company("Finteco");
var person1 = new Person(1, "Eduard", company);
var person2 = person1.Clone() as Person;
person2.Company.Name = "Apple";

Console.WriteLine(person1.Company.Name); // finteco

class Person : ICloneable
{
    public int Id { get; set; }
    public string Name { get; set; }
    public Company Company { get; set; }

    public Person(int id, string name, Company company)
    {
        Id = id;
        Name = name;
        Company = company;
    }

    public object Clone() => new Person(Id, Name, new Company(Company.Name));
}

class Company
{
    public string Name { get; set; }

    public Company(string name)
    {
        Name = name;
    }
}
```

```csharp
// плохой пример

var company = new Company("Finteco");
var person1 = new Person(1, "Eduard", company);
var person2 = person1.Clone() as Person;
person2.Company.Name = "Apple";

Console.WriteLine(person1.Company.Name); // Apple

class Person : ICloneable
{
    public int Id { get; set; }
    public string Name { get; set; }
    public Company Company { get; set; }

    public Person(int id, string name, Company company)
    {
        Id = id;
        Name = name;
        Company = company;
    }

    public object Clone() => MemberwiseClone();
}

class Company : ICloneable
{
    public string Name { get; set; }

    public Company(string name)
    {
        Name = name;
    }

    public object Clone() => MemberwiseClone();
}
```

# 8. Сортировка объектов. Интерфейс IComparable

- метод сорт по умолчанию работает для примитивных типов
- для сортировки сложных объектов применяется интерфейс IComparable

```csharp
var numbers = new int[] { 5, 2, 1, 6, 8, 3 };
Array.Sort(numbers);

foreach (var number in numbers)
{
    Console.WriteLine(number);
}
```

- сравнивает текущий объект с объектом переданным в параметре. Возвращает целое число
- имеет обобщенную версию

```csharp
public interface IComparable
{
    int CompareTo(object? o);
}
```

```csharp
var number1 = new Number(1);
var number2 = new Number(2);
var number3 = new Number(3);

var numbers = new Number[] { number3, number2, number1 };
Array.Sort(numbers);

foreach (var number in numbers)
{
    Console.WriteLine(number.Value);
}

class Number : IComparable
{
    public int Value { get; set; }

    public Number(int value)
    {
        Value = value;
    }

    public int CompareTo(object? obj)
    {
        if (obj is Number number)
        {
            return this.Value.CompareTo(number.Value);
        }

        throw new Exception();
    }
}
```

## 8.1 Применение компаратора

- предназначен для сравнение двух объектов
- надо создать отдельный класс компаратора
- объект компоратора указывается в качестве второго параметра в методе Sort, при этом неважно реализует ли класс Number интерфейс IComparable или нет
- правила сортировки компаратора имеют больший приоритет

```csharp
public interface IComparer<in T>
{
    int Compare(T? x, T? y);
}
```

```csharp
var number1 = new Number(1);
var number2 = new Number(2);
var number3 = new Number(3);

var numbers = new Number[] { number3, number2, number1 };
Array.Sort(numbers, new NumberComparer());

foreach (var number in numbers)
{
    Console.WriteLine(number.Value);
}

class Number
{
    public int Value { get; set; }

    public Number(int value)
    {
        Value = value;
    }
}

class NumberComparer : IComparer<Number>
{
    public int Compare(Number? x, Number? y)
    {
        if (x is null || y is null)
        {
            throw new Exception();
        }

        return x.Value.CompareTo(y.Value);
    }
}
```

# 9. Ковариантность и контравариантность обобщенных интерфейсов

- эти понятия связаны с возможностью использования в приложении вместо некоторого типа другой тип, который находится нижу или выше в иерархии наследования
- коваринтность - позволяет использовать более конкретный тип, чем заданный изначально
- контравариантность позволяет использоовать более универсальный тип, чем заданный изначально
- инваринтность - позволяет использовать только заданный тип
- C# позволяет использовать эти понятия в обобщенных интерфейсах. Такая функциональность повышает гибкость при использовании обобщенных интерфейсов

## 9.1 Ковариантные интерфейсы

- в обобщенных интерфейсах к универсальному параметру ключевое слово out
- такой универсальный параметр используется только в качестве возвращаемого значения методов интерфейса

```csharp
IEmailSender<Message> messenger1 = new EmailSender();
Message message = messenger1.Send("message");
Console.WriteLine(message.Text);

IEmailSender<EmailMessage> messenger2 = new EmailSender();
EmailMessage emailMessage = messenger2.Send("emailMessage");
Console.WriteLine(emailMessage.Text);

class Message
{
    public string Text { get; set; }

    public Message(string text)
    {
        Text = text;
    }
}

class EmailMessage : Message
{
    public EmailMessage(string text)
        : base(text) { }
}

interface IEmailSender<out T>
{
    T Send(string message);
}

class EmailSender : IEmailSender<EmailMessage>
{
    public EmailMessage Send(string message)
    {
        return new EmailMessage($"Message: {message}");
    }
}
```

## 9.2 Контравариантные интерфейсы

- в обобщенных интерфейсах к универсальному параметру ключевое слово in
- такой универсальный параметр применяется только к аргументам метода

```csharp
IEmailSender<Message> messenger1 = new EmailSender();
Message message = new Message("message");
messenger1.Send(message);

IEmailSender<EmailMessage> messenger2 = new EmailSender();
EmailMessage emailMessage = new EmailMessage("emailMessage");
messenger2.Send(emailMessage);

class Message
{
    public string Text { get; set; }

    public Message(string text)
    {
        Text = text;
    }
}

class EmailMessage : Message
{
    public EmailMessage(string text)
        : base(text) { }
}

interface IEmailSender<in T>
{
    void Send(T message);
}

class EmailSender : IEmailSender<Message>
{
    public void Send(Message message)
    {
        Console.WriteLine($"Message: {message}");
    }
}
```

## 9.3 Совмещение ковариантности и контравариантности

```csharp
IEmailSender<Message, EmailMessage> messenger1 = new EmailSender();
Message message = new Message("message");
EmailMessage emailMessage = new EmailMessage("emailMessage");
messenger1.SendMessage(message);
messenger1.SendMessage(emailMessage);

IEmailSender<Message, EmailMessage> messenger2 = new EmailSender();
Message message2 = messenger2.WriteMessage("message");
Console.WriteLine(message2.Text);
EmailMessage emailMessage2 = messenger2.WriteMessage("emailMessage");
Console.WriteLine(emailMessage2.Text);

class Message
{
    public string Text { get; set; }

    public Message(string text)
    {
        Text = text;
    }
}

class EmailMessage : Message
{
    public EmailMessage(string text)
        : base(text) { }
}

interface IEmailSender<in T, out K>
{
    void SendMessage(T message);
    K WriteMessage(string text);
}

class EmailSender : IEmailSender<Message, EmailMessage>
{
    public void SendMessage(Message message)
    {
        Console.WriteLine($"Отправляется сообщение: {message.Text}");
    }

    public EmailMessage WriteMessage(string text)
    {
        return new EmailMessage($"Email: {text}");
    }
}
```
