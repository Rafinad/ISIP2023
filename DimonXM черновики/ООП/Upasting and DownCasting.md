UpCast:
- Нужен для создания коллекций классов, например массива или вектора, на основе базового класса
- Позволяет обращаться к переопределенным методам через базовый класс, подменяя ссылку
- Позволяет обращаться к общим методам
Пример:
```c++
#include <iostream>
#include <vector>
using namespace std;

class Animal {
public:
    // Виртуальный метод - общий интерфейс для всех животных
    virtual void makeSound() const = 0; // Чисто виртуальная функция (интерфейс)
    virtual ~Animal() {} // Виртуальный деструктор
};

class Dog : public Animal {
public:
    void makeSound() const override {
        cout << "Woof!" << endl;
    }
    void fetch() const { /* ... */ }
};

class Cat : public Animal {
public:
    void makeSound() const override {
        cout << "Meow!" << endl;
    }
    void climbTree() const { /* ... */ }
};

class Cow : public Animal {
public:
    void makeSound() const override {
        cout << "Moo!" << endl;
    }
};

// Ключевой момент! Функция принимает ссылку на ЛЮБОЕ животное
void hearSound(const Animal& animal) {
    animal.makeSound(); // Вызовется правильная версия метода благодаря полиморфизму
}

int main() {
    Dog fido;
    Cat whiskers;
    Cow bessie;

    // Upcast происходит неявно при передаче объекта в функцию
    hearSound(fido);      // Вывод: Woof!
    hearSound(whiskers);  // Вывод: Meow!
    hearSound(bessie);    // Вывод: Moo!

    return 0;
}
```

```cpp
Dog* myDog = new Dog();
Animal* myAnimal = myDog; // <- Сам upcast (техническое действие)
```

DownCast:
- Нужен для приведения указателя/ссылки базового класса к производному классу
- Позволяет получить доступ к специфическим методам производного класса
- Требует проверки типа во время выполнения (dynamic_cast)
- Может быть опасен, если объект не является экземпляром целевого класса

Пример:
```cpp
#include <iostream>
#include <vector>
#include <memory>
using namespace std;

class Animal {
public:
    virtual void makeSound() const = 0;
    virtual ~Animal() {}
};

class Dog : public Animal {
public:
    void makeSound() const override {
        cout << "Woof!" << endl;
    }
    void fetch() const {
        cout << "Dog is fetching the ball!" << endl;
    }
};

class Cat : public Animal {
public:
    void makeSound() const override {
        cout << "Meow!" << endl;
    }
    void climbTree() const {
        cout << "Cat is climbing the tree!" << endl;
    }
};

class Cow : public Animal {
public:
    void makeSound() const override {
        cout << "Moo!" << endl;
    }
    void giveMilk() const {
        cout << "Cow is giving milk!" << endl;
    }
};

// Функция для демонстрации downcast
void processAnimal(Animal* animal) {
    animal->makeSound(); // Общий метод через базовый класс
    
    // Downcast с проверкой типа
    if (Dog* dog = dynamic_cast<Dog*>(animal)) {
        dog->fetch(); // Специфический метод Dog
    }
    else if (Cat* cat = dynamic_cast<Cat*>(animal)) {
        cat->climbTree(); // Специфический метод Cat
    }
    else if (Cow* cow = dynamic_cast<Cow*>(animal)) {
        cow->giveMilk(); // Специфический метод Cow
    }
    else {
        cout << "Unknown animal type" << endl;
    }
}

int main() {
    vector<unique_ptr<Animal>> animals;
    animals.push_back(make_unique<Dog>());
    animals.push_back(make_unique<Cat>());
    animals.push_back(make_unique<Cow>());
    
    // Upcast происходит автоматически при создании unique_ptr<Animal>
    for (const auto& animal : animals) {
        processAnimal(animal.get()); // <- Здесь происходит downcast
    }
    
    // Явный downcast пример:
    Animal* myAnimal = new Dog(); // <- Upcast
    Dog* myDog = dynamic_cast<Dog*>(myAnimal); // <- Downcast с проверкой
    
    if (myDog) {
        myDog->fetch(); // Безопасный доступ к специфическому методу
    }
    
    delete myAnimal;
    return 0;
}
```
