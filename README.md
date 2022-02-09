# 스위프트로 이해하는 디자인 패턴 👨🏻‍🎨

GoF 디자인 패턴을 스위프트로 구현해가며 정리하는 저장소입니다.

## Table of Contents 

|생성 패턴|구조 패턴|행위 패턴|
|:-:|:-:|:-:|
|추상 팩토리(Abstract Factory)|어댑터(Adapter)|책임 연쇄(Chain of Responsibility)|
|빌더(Builder)|브릿지(Bridge)|커맨드(Command)|
|[팩토리 메서드(Factory Methods)](https://github.com/jeonyeohun/Design-Patterns-In-Swift/edit/main/README.md#-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C-%ED%8C%A8%ED%84%B4-factory-method-pattern)|컴포지트(Composite)|인터프리터(Interpreter)|
|프로토타입(Prototype)|퍼사드(Facade)|미디에이터(Mediator)|
|싱글톤(Singleton)|플라이웨이트(Flyweight)|메멘토(Memento)|
||프록시(Proxy)|옵저버(Observer)|
||[데코레이터(Decorator)](https://github.com/jeonyeohun/Design-Patterns-In-Swift/edit/main/README.md#-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C-%ED%8C%A8%ED%84%B4-factory-method-pattern)|스테이트(State)|
|||[전략(Strategy)](https://github.com/jeonyeohun/Design-Patterns-In-Swift/edit/main/README.md#%EF%B8%8F-%EC%A0%84%EB%9E%B5-%ED%8C%A8%ED%84%B4-strategy-pattern)|
|||템플릿 메서드(Template Method)|
|||방문(Visitor)|

<br/>

## 생성 패턴(Creational Pattern)

### 🏭 팩토리 메서드 패턴 (Factory Method Pattern)

팩토리 메서드 패턴은 객체의 인스턴스를 생성하는 인터페이스를 제공하고, 인스턴스의 생성은 서브클래스에서 정의하도록 하는 방법입니다. 따라서 인스턴스가 생성될 때는 입력에 따른 적절한 팩토리 객체를 선택해 해당 객체를 통해 인스턴스를 생성하게 됩니다. [더 자세히 알아보기](https://jeonyeohun.tistory.com/385)

```swift
import Foundation

protocol Animal {
    var name: String { get set }
    func sound()
}

class Dog: Animal {
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func sound() {
        print("\(name) Bark! 🐶")
    }
}

class Cat: Animal {
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func sound() {
        print("\(name) Meow! 😸")
    }
}

protocol AnimalFactory {
    func make(with name: String) -> Animal
}

class RandomAnimalFactory: AnimalFactory {
    func make(with name: String) -> Animal {
        return Int.random(in: 0...1) == 0 ? Dog(name: name) : Cat(name: name)
    }
}

class EvenAnimalFactory: AnimalFactory {
    var previousState: Animal.Type?
    func make(with name: String) -> Animal {
        if previousState == Cat.self {
            self.previousState = Dog.self
            return Dog(name: name)
        } else {
            self.previousState = Cat.self
            return Cat(name: name)
        }
    }
}

class AnimalCafe {
    private var animals = [Animal]()
    private var factory: AnimalFactory
    
    init(factory: AnimalFactory) {
        self.factory = factory
    }
    
    func addAnimal(with name: String) {
        self.animals.append(self.factory.make(with: name))
    }
    
    func printAnimals() {
        self.animals.forEach { animal in
            animal.sound()
        }
    }
    
    func change(factory: AnimalFactory) {
        self.factory = factory
    }
    
    func clear() {
        self.animals = []
    }
}

let animalCafe = AnimalCafe(factory: EvenAnimalFactory())
animalCafe.addAnimal(with: "A")
animalCafe.addAnimal(with: "B")
animalCafe.addAnimal(with: "C")
animalCafe.addAnimal(with: "D")
animalCafe.addAnimal(with: "E")
animalCafe.addAnimal(with: "F")
animalCafe.printAnimals()

animalCafe.clear()
print("\n## Change Factory ##\n")
animalCafe.change(factory: RandomAnimalFactory())
animalCafe.addAnimal(with: "A")
animalCafe.addAnimal(with: "B")
animalCafe.addAnimal(with: "C")
animalCafe.addAnimal(with: "D")
animalCafe.addAnimal(with: "E")
animalCafe.addAnimal(with: "F")
animalCafe.printAnimals()

//A Meow! 😸
//B Bark! 🐶
//C Meow! 😸
//D Bark! 🐶
//E Meow! 😸
//F Bark! 🐶
//
//## Change Factory ##
//
//A Meow! 😸
//B Bark! 🐶
//C Bark! 🐶
//D Meow! 😸
//E Meow! 😸
//F Meow! 😸

```

<br/>

## 구조 패턴(Structural Pattern)

### 👨🏻‍🎨 데코레이터 패턴 (Decorator Pattern)
데코레이터 패턴은 데코레이터를 통해 유연하게 새로운 기능이나 책임을 추가할 수 있게 하는 패턴입니다. [더 자세히 알아보기](https://jeonyeohun.tistory.com/382)

```swift
import Foundation

protocol Sandwich: CustomStringConvertible {
    func cost () -> Int
}

protocol Decorating: Sandwich {
    var sandwich: Sandwich { get set }
}

class OriginalSandwich: Sandwich {
    var description: String {
        return "OriginalSandwich"
    }
    
    func cost() -> Int {
        return 3000
    }
}

class VeganSandwich: Sandwich {
    var description: String {
        return "VeganSandwich"
    }
    
    func cost() -> Int {
        return 2500
    }
}


class Avocado: Decorating {
    var sandwich: Sandwich
    var description: String {
        return self.sandwich.description + " + Avocado"
    }
    
    init(sandwich: Sandwich) {
        self.sandwich = sandwich
    }
    
    func cost() -> Int {
        return self.sandwich.cost() + 1000
    }
}

class MeatBall: Decorating {
    var sandwich: Sandwich
    var description: String {
        return self.sandwich.description + " + MeatBall"
    }
    
    init(sandwich: Sandwich) {
        self.sandwich = sandwich
    }
    
    func cost() -> Int {
        return self.sandwich.cost() + 1500
    }
}

class Tomato: Decorating {
    var sandwich: Sandwich
    var description: String {
        return self.sandwich.description + " + Tomato"
    }
    
    init(sandwich: Sandwich) {
        self.sandwich = sandwich
    }
    
    func cost() -> Int {
        return self.sandwich.cost() + 500
    }
}

let avocadoMeatBallSandwich = MeatBall(sandwich: Avocado(sandwich: OriginalSandwich()))
print(avocadoMeatBallSandwich.cost()) // 5500
print(avocadoMeatBallSandwich) // OriginalSandwich + Avocado + MeatBall

let tomatoAdded = Tomato(sandwich: avocadoMeatBallSandwich)
print(tomatoAdded.cost()) // 6000
print(tomatoAdded) // OriginalSandwich + Avocado + MeatBall + Tomato
```

<br/>

## 행위 패턴(Behavioral Pattern)

### ⚡️ 전략 패턴 (Strategy Pattern)

전략 패턴은 비슷한 목적의 알고리즘을 전략이라는 인터페이스로 캡슐화하고, 클라이언트로부터 알고리즘 객체를 분리해 런타임에 알고리즘을 변경할 수 있게 합니다. 또한 클라이언트와 알고리즘 객체의 느슨한 연결을 통해 수정과 변경이 용이한 구조를 만들 수 있게 하는 디자인 패턴입니다. [더 자세히 알아보기](https://jeonyeohun.tistory.com/379)

```swift
import Foundation

protocol Validatable {
    func validate(text: String) -> Bool
}

protocol Validator {
    var validationStrategy: Validatable { get set }
    func validate(text: String) -> Bool
}

final class StringValidator: Validator {
    var validationStrategy: Validatable
    
    init(strategy: Validatable) {
        self.validationStrategy = strategy
    }
    
    func change(strategy: Validatable) {
        self.validationStrategy = strategy
    }
    
    func validate(text: String) -> Bool {
        return validationStrategy.validate(text: text)
    }
}

class NumberValidator: Validatable {
    func validate(text: String) -> Bool {
        return text.allSatisfy({ $0.isNumber })
    }
}

class LengthValidator: Validatable {
    func validate(text: String) -> Bool {
        return text.count < 10
    }
}

class AsciiValidator: Validatable {
    func validate(text: String) -> Bool {
        return text.allSatisfy({ $0.isASCII })
    }
}

let validator = StringValidator(strategy: LengthValidator())
print(validator.validate(text: "12345678910")) // false

validator.change(strategy: NumberValidator())
print(validator.validate(text: "12345678910")) // true

validator.change(strategy: AsciiValidator())
print(validator.validate(text: "12345678910")) // true

func validateAll(text: String) -> Bool {
    let strategies: [Validatable] = [LengthValidator(), NumberValidator(), AsciiValidator()]
    return strategies.filter({ strategy in
        return StringValidator(strategy: strategy).validate(text: text)
    }).isEmpty
}

print(validateAll(text: "12345678910")) // false

```




