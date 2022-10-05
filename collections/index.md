# 1. List

Плюсы списков:

- массивы - хранят наборы однотипных данных, которые хранят фиксированное кол-во объектов
- некоторые их них реализуют стандартные структуры данных: стек, очередь, словарь

List:

- класс
- System.Collections.Generic
- список однотипных объектов

```csharp
// пустой
var list1 = new List<string>();
// проинициализирован
var list2 = new List<string>() { "Eduard", "Emma" };
// проинициализирован значениями из другого списка
var list3 = new List<string>(list2);
// проинициализирован значениями из другого списка, также определяет свои
var list4 = new List<string>(list3) { "Vitaly" };

var list5 = new List<Person>()
{
    new Person("Eduard"),
    new Person("Emma")
};

class Person
{
    private string _name { get; set; }

    public Person(string name)
    {
        _name = name;
    }
}
```

## 1.1 Установка начальной емкости списка

- позволяет увеличить производительность и уменьшить издержки на выделение памяти при добавлении элементов
- динамическое добавление в список может на низком уровне привести к дополнительному выделению памяти, что снижает производительность

```csharp
var list1 = new List<string>(5); // установка в конструкторе

var list2 = new List<string>();
list2.Capacity = 5; // установка через свойство
```

## 1.2 Обращение к элементам списка

- как и массивы, списки поддерживают индексы

```csharp
var list1 = new List<string>(5);
list1.Add("string 1");
list1.Add("string 2");
list1.Add("string 3");
list1.Add("string 4");
list1.Add("string 5");

Console.WriteLine(list1[3]);
```

## 1.3 Длина списка

- с помощью свойства Count

```csharp
var list1 = new List<string>(5);
list1.Add("string 1");
list1.Add("string 2");
list1.Add("string 3");
list1.Add("string 4");
list1.Add("string 5"); // capacity: 5
list1.Add("string 6");
list1.Add("string 7");
list1.Add("string 8");
list1.Add("string 9");
list1.Add("string 10"); // capacity: 10
list1.Add("string 11");
list1.Add("string 12");
list1.Add("string 13");
list1.Add("string 14");
list1.Add("string 15");
list1.Add("string 16");
list1.Add("string 17");
list1.Add("string 18");
list1.Add("string 19");
list1.Add("string 20"); // capacity: 20
list1.Add("string 21"); // capacity: 40

Console.WriteLine(list1.Count); // 21
Console.WriteLine(list1.Capacity); // 40
```

## 1.4 Перебор списка

```csharp
var list = new List<string>() { "str 1", "str 2", "str 3" };

for (int i = 0; i < list.Count; i++)
{
    Console.WriteLine(list[i]);
}

foreach (var item in list)
{
    Console.WriteLine(item);
}
```

## 1.5 Методы списка

- void Add(T item)
- void AddRange(IEnumerable<T> collection)

```csharp
var list = new List<int> { 1, 2, 3, 4 };
list.Add(5); // 1, 2, 3, 4, 5

list.AddRange(new List<int> { 1, 2 }); // 1, 2, 3, 4, 5, 1, 2
list.AddRange(new int[] { 3, 4 }); // 1, 2, 3, 4, 5, 1, 2, 3, 4
```

- ReadOnlyCollection<T> AsReadOnly()

```csharp
var list1 = new List<int> { 1, 2, 3, 4 };
list1[0] = 5;

var list2 = new List<int> { 1, 2, 3, 4 }.AsReadOnly();
list2[0] = 3; // error
```

- void Clear()

```csharp
var list = new List<int> { 1, 2, 3, 4 };

list.Clear();

Console.WriteLine(list.Count); // 0
Console.WriteLine(list.Capacity); // 4
```

- bool Contains(T item)

```csharp
var list = new List<int> { 1, 2, 3, 4 };

Console.WriteLine(list.Contains(1)); // true
Console.WriteLine(list.Contains(5)); // false
```

- void CopyTo(T[] array)

```csharp
var list = new List<int> { 1, 2, 3, 4 };

var array1 = new int[4];

list.CopyTo(array1);
```

- bool Exists(Predicate<T> match)

```csharp
var list = new List<int> { 1, 2, 3, 4 };

var result = list.Exists(i => i == 1); // true
```

- T? Find(Predicate<T> match)
- List<T> FindAll(Predicate<T> match)
- int FindIndex(Predicate<T> match)
- T? FindLast(Predicate<T> match)
- int FindLastIndex(Predicate<T> match)

```csharp
var list = new List<int> { 1, 2, 3, 1, 4 };

var result1 = list.Find(x => x == 1); // 1
var result2 = list.Find(x => x == 6); // 0

var result3 = list.FindAll(x => x == 1); // 1, 1
var result4 = list.FindAll(x => x == 6); // 1, 1

var result5 = list.FindIndex(x => x == 4); // 4
var result6 = list.FindIndex(x => x == 6); // -1

var result7 = list.FindLast(x => x == 1); // 1
var result8 = list.FindLast(x => x == 6); // 0

var result9 = list.FindLastIndex(x => x == 1); // 3
var result10 = list.FindLastIndex(x => x == 6); // -1
```

- List<T> GetRange(int index, int count)

```csharp
var list = new List<int> { 1, 2, 3, 1, 4 };

var result = list.GetRange(1, 3); // 2, 3, 1
```

- int IndexOf(T item)
- int LastIndexOf(T item)
- `Чем отличается от FindIndex?`, тем что IndexOf ищет по элементу, а FindIndex - по предикату

```csharp
var list = new List<int> { 1, 2, 3, 1, 4 };

var result1 = list.IndexOf(2); // 1
var result2 = list.IndexOf(7); // -1

var result3 = list.LastIndexOf(1); // 3
var result4 = list.LastIndexOf(7); // -1
```

- bool Remove(T item) - удаляет первый найденный элемент
- int RemoveAll(Predicate<T> match)
- void RemoveAt(int index)
- void RemoveRange(int index, int count)

```csharp
var list = new List<int> { 1, 2, 3, 1, 4 };

var result1 = list.Remove(1); // true
var result2 = list.Remove(7); // false

var result = list.RemoveAll(x => x == 1); // 2

list.RemoveAt(0); // 2, 3, 1, 4
list.RemoveRange(0, 2); // 1, 4
```

- void Reverse()
- void Reverse(int index, int count)

```csharp
var list = new List<int> { 1, 2, 3, 1, 4 };

list.Reverse(); // 4, 1, 3, 2, 1
list.Reverse(0, 3); // 3, 1, 4, 2 ,1
```

- int BinarySearch(T item) - список должен быть отсортирован, бинарный поиск, возвращает индекс элемента в списке
- List<TOutput> ConvertAll<TOutput>(Converter<T, TOutput> converter)
- void Sort() - сортировка
- void Sort(IComparer<T>? comparer) - сортировка с помощью объекта comparer

# 2. Двухсвязный список LinkedList<T>

- двухсвязный список, в котором каждый элемент хранит ссылку одновременно на следующий и на предыдущий элемент

```csharp
var list = new List<int>() { 1, 2, 3 };

var linkedList1 = new LinkedList<int>();
var linkedList2 = new LinkedList<int>(list);
```

## 2.1 LinkedListNode

- в простом списке List<T> каждый элемент представляет объект типа T, то в LinkedList<T> каждый узел представляет объект класса LinkedListNode<T>

LinkedListNode свойства:

- Value - возвращает или устанавливает само значение
- Next - возвращает ссылку на следующий элемент типа LinkedListNode<T> в списке, если не найдет, то null
- Previous - возвращает ссылку на предыдущий элемент типа LinkedListNode<T> в списке, если не найдет, то null

## 2.2 LinkedList свойства

- Count - кол-во элементов в связанном списке
- First - возвращает первый узел в списке в виде LinkedListNode
- Last - возвращает последний узел в списке в виде LinkedListNode

```csharp
var list = new List<int>() { 1, 2, 3 };

var linkedList = new LinkedList<int>(list);

// 3
// System.Collections.Generic.LinkedListNode`1[System.Int32]
// System.Collections.Generic.LinkedListNode`1[System.Int32]

Console.WriteLine(linkedList.Count);
Console.WriteLine(linkedList.First);
Console.WriteLine(linkedList.First?.Value); // 1
Console.WriteLine(linkedList.First?.Next?.Value); // 2
Console.WriteLine(linkedList.Last);
```

```csharp
var list = new List<int>() { 1, 2, 3 };

var linkedList = new LinkedList<int>(list);

var currentNode = linkedList.First;

while (currentNode != null)
{
    Console.WriteLine(currentNode.Value); // 1, 2, 3
    currentNode = currentNode.Next;
}

currentNode = linkedList.Last;

while (currentNode != null)
{
    Console.WriteLine(currentNode.Value); // 3, 2, 1
    currentNode = currentNode.Previous;
}
```

## 2.3 Методы LinkedList

- void AddAfter(LinkedListNode<T> node, LinkedListNode<T> newNode)

```csharp
var list = new LinkedList<string>();
list.AddFirst("Eduard");
list.AddLast("Emma");

if (list.First != null)
{
    list.AddAfter(list.First, "Tom");
}

Print(list); // Eduard, Tom, Emma
```

- void AddBefore(LinkedListNode<T> node, LinkedListNode<T> newNode)

```csharp
var list = new LinkedList<string>();
list.AddFirst("Eduard");
list.AddLast("Emma");

if (list.Last != null)
{
    list.AddBefore(list.Last, "Vitaly");
}

Print(list); // Eduard, Vitaly, Emma
```

- void AddFirst(LinkedListNode<T> node)

```csharp
var list = new LinkedList<string>();
list.AddFirst("Eduard");
list.AddFirst("Emma");

Print(list); // Emma, Eduard
```

- void AddLast(LinkedListNode<T> node)

```csharp
var list = new LinkedList<string>();
list.AddFirst("Eduard");
list.AddLast("Emma");

Print(list); // Eduard, Emma
```

- LinkedListNode<T>? Find(T value)

```csharp
var list = new LinkedList<string>();
list.AddFirst("Eduard");

var result1 = list.Find("Eduard");
var result2 = list.Find("Emma");

Console.WriteLine(result1?.Value); // Eduard
Console.WriteLine(result2?.Value); // null
```

- LinkedListNode<T>? FindLast(T value)

```csharp
var list = new LinkedList<string>();
list.AddFirst("Eduard");

var result1 = list.FindLast("Eduard");
var result2 = list.FindLast("Emma");

Console.WriteLine(result1?.Value); // Eduard
Console.WriteLine(result2?.Value); // null
```

- void Remove(LinkedListNode<T> node)
- void RemoveFirst()
- void RemoveLast()

# 3. Queue

- класс, который пердставляет очередь, работающая по алгоритму FIFO

## 3.1 Создание очереди

- пустая очередь
- пустая очередь с емкостью
- очередь инициализированная элементами из другой коллекции или списка

```csharp
var queue1 = new Queue<int>();

var queue2 = new Queue<int>(10);

var list = new List<int>() { 1, 2, 3 };
var queue3 = new Queue<int>(list);

Console.WriteLine(queue3.Count); // получение кол-ва элементов

foreach (var item in queue3) // перебор очереди
{
    Console.WriteLine(item);
}
```

## 3.2 Методы Queue

- void Clear()
- bool Contains(T item)
- void CopyTo(T[] array, int arrayIndex)
- T Dequeue() - удаляет и возвращает объект очереди из начала
- void Enqueue(T item) - добавляет объект в конец
- T Peek() - возвращает объект очереди из начала не удаляя его
- bool TryDequeue([MaybeNullWhen(false)] out T result) - удаляет объект из начала очереди и копирует его в параметр, возвращает true - если объект удален
- bool TryPeek([MaybeNullWhen(false)] out T result) - если объект присутсвует, то копирует его значение в параметр, возвращает true и объект не удаляется

```csharp
var queue = new Queue<int>();

queue.Enqueue(1);
queue.Enqueue(2);
queue.Enqueue(3);
queue.Enqueue(4);

Print(queue); // 1, 2, 3, 4

var result1 = queue.Dequeue(); // если очередь пуста, то исключение
Console.WriteLine($"Result: {result1}"); // 1

Print(queue); // 2, 3, 4

var result2 = queue.Peek(); // если очередь пуста, то исключение
Console.WriteLine($"Result: {result2}"); // 2

Print(queue); // 2, 3, 4


Console.WriteLine(queue.TryDequeue(out int result3)); // true
Console.WriteLine($"Result: {result3}"); // 2

Print(queue); // 3, 4

Console.WriteLine(queue.TryPeek(out int result4)); // true
Console.WriteLine($"Result: {result4}"); // 3

Print(queue); // 3, 4

void Print(Queue<int> queue)
{
    foreach (var item in queue)
    {
        Console.WriteLine(item);
    }
    Console.WriteLine();
}
```

# 4. Stack

- класс, представляющий собой коллекцию, которая использует алгоритм LIFO

## 4.1 Создание стека

```csharp
var stack1 = new Stack<int>();

var stack2 = new Stack<int>(5);

var list = new List<int>() { 1, 2, 3 };
var stack3 = new Stack<int>(list);

Console.WriteLine(stack3.Count); // 3

foreach(var item in stack3)
{
    Console.WriteLine(item); // 3, 2, 1
}
```

## 4.2 Методы стека

- T Peek() - извлекает и не удаляет элемент из верхушки стека
- T Pop() - извлекает и удаляет элемент из верхушки стека
- void Push(T item) - добавляет элемент в верхушку стека
- bool TryPeek([MaybeNullWhen(false)] out T result)
- bool TryPop([MaybeNullWhen(false)] out T result)

```csharp
var stack = new Stack<int>();

stack.Push(1);
stack.Push(2);
stack.Push(3);
stack.Push(4);

Print(stack); // 4, 3, 2, 1

var result1 = stack.Pop();

Console.WriteLine($"Result: {result1}"); // 4

Print(stack); // 3, 2, 1

var result2 = stack.Peek();

Console.WriteLine($"Result: {result2}"); // 3

Print(stack); // 3, 2, 1

Console.WriteLine(stack.TryPeek(out int result3)); // true

Console.WriteLine($"Result: {result3}"); // 3

Print(stack); // 3, 2, 1

void Print(Stack<int> stack)
{
    foreach (var item in stack)
    {
        Console.WriteLine(item);
    }
    Console.WriteLine();
}
```

# 5. Dictionary

- https://habr.com/ru/post/198104/
- https://habr.com/ru/post/488836/

- словарь - хранит объекты, которые представляют пару ключ-значение
- каждый элемент в словаре представляет объект KeyValuePair<TKey, TValue>
- если установить значение по ключу, который уже существует, то значение пересоздастся

```csharp
var dictionary1 = new Dictionary<int, string>();

var dictionary2 = new Dictionary<int, string>()
{
    {1, "Eduard" },
    {2, "Emma" },
    {3, "Tom" }
};

var dictionary3 = new Dictionary<int, string>()
{
    [1] = "Eduard",
    [2] = "Emma",
    [3] = "Tom"
};

var keyValuePair1 = new KeyValuePair<int, string>(23, "Eduard");
var keyValuePair2 = new KeyValuePair<int, string>(22, "Emma");

var list = new List<KeyValuePair<int, string>>() { keyValuePair1, keyValuePair2 };

var dictionary4 = new Dictionary<int, string>(list);

// Key: 23, Value: Eduard
// Key: 22, Value: Emma
Print(dictionary4);

var emma = dictionary4[22];
Console.WriteLine(emma); // Emma

dictionary4[22] = "Tom";
Console.WriteLine(dictionary4[22]); // Tom

void Print(Dictionary<int, string> dictionary)
{
    foreach(var item in dictionary)
    {
        Console.WriteLine($"Key: {item.Key}, Value: {item.Value}");
    }
}
```

## 5.1 Методы и свойства Dictionary

- void Add(K key, V value) - если элемент по такому ключу существует, то исключение
- bool ContainsKey(K key)
- bool ContainsValue(V value)
- bool Remove(K key) - если такого элемента нет, то false
- bool Remove(K key, out V value) - удаляет элемент по ключу, а удаленный элемент помещается в выходной параметр
- bool TryGetValue(K key, out V value)
- bool TryAdd(K key, V value) - добавляет элемент в словарь, при успешном добавлении возвращает true

```csharp
var dictionary = new Dictionary<int, string>()
{
    {1, "Eduard" },
    {2, "Emma" },
    {3, "Tom" }
};

dictionary.Add(4, "Kirill");

Print(dictionary);

Console.WriteLine(dictionary.ContainsKey(1)); // true
Console.WriteLine(dictionary.ContainsValue("Eduard")); // true
Console.WriteLine(dictionary.ContainsValue("Emma2")); // false

Console.WriteLine(dictionary.Remove(1)); // true

Print(dictionary);

Console.WriteLine(dictionary.Remove(2, out string? result)); // true
Console.WriteLine($"Result: {result}"); // Emma

void Print(Dictionary<int, string> dictionary)
{
    foreach (var item in dictionary)
    {
        Console.WriteLine($"Key: {item.Key}, Value: {item.Value}");
    }
}
```

# 6. Интерфейсы IEnumerable и IEnumerator

- для большинства коллекций основной является реализация интерфейсов IEnumerable и IEnumerator, благодаря такой реализации мы можем перебирать объекты в цикле foreach
- перебираемая коллекция должна реализовывать интерфейс IEnumerable

```csharp
public interface IEnumerable
{
    IEnumerator GetEnumerator();
}
```

- IEnumerable имеет метод, возвращающий ссылку IEnumerator - перечислитель

```csharp
public interface IEnumerator
{
    bool MoveNext(); // перемещение на одну позицию вперед в контейнере элементов, если позиция еще есть, возвращает true
    object Current {get;}  // текущий элемент в контейнере
    void Reset(); // перемещение в начало контейнера
}
```

- переберем массив без использования цикла с помощью интерфейса IEnumerator

```csharp
var array = new int[] { 1, 2, 3 };  

var enumerator = array.GetEnumerator();

while (enumerator.MoveNext())
{
    Console.WriteLine($"Current: {enumerator.Current}");
}

enumerator.Reset();
```

## 6.1 Реализация IEnumerable и IEnumerator

```csharp
using System.Collections;

var number = new Number();

foreach(var item in number)
{
    Console.WriteLine(item);
}

class Number : IEnumerable // наследоваться не обязательно
{
    private int[] _numbers = new int[] { 1, 2, 3, 4, 5, 6 };

    // можно создать тоже самое, и без реализации интерфейса
    public IEnumerator GetEnumerator() => _numbers.GetEnumerator(); 
}
```

# 7. Итераторы и оператор yield

