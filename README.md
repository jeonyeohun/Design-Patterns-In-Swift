# 스위프트로 이해하는 디자인 패턴 👨🏻‍🎨

GoF 디자인 패턴을 스위프트로 구현해가며 정리하는 저장소입니다.

## Table of Contents 

|생성 패턴|구조 패턴|행위 패턴|
|:-:|:-:|:-:|
|[추상 팩토리(Abstract Factory)](https://github.com/jeonyeohun/Design-Patterns-In-Swift#-%EC%B6%94%EC%83%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%ED%8C%A8%ED%84%B4-abstract-factory-pattern)|어댑터(Adapter)|책임 연쇄(Chain of Responsibility)|
|빌더(Builder)|브릿지(Bridge)|[커맨드(Command)](https://github.com/jeonyeohun/Design-Patterns-In-Swift#-%EC%BB%A4%EB%A7%A8%EB%93%9C-%ED%8C%A8%ED%84%B4)|
|[팩토리 메서드(Factory Methods)](https://github.com/jeonyeohun/Design-Patterns-In-Swift#-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C-%ED%8C%A8%ED%84%B4-factory-method-pattern)|컴포지트(Composite)|인터프리터(Interpreter)|
|프로토타입(Prototype)|퍼사드(Facade)|미디에이터(Mediator)|
|[싱글톤(Singleton)](https://github.com/jeonyeohun/Design-Patterns-In-Swift#%EF%B8%8F-%EC%8B%B1%EA%B8%80%ED%86%A4-%ED%8C%A8%ED%84%B4-singleton-pattern)|플라이웨이트(Flyweight)|메멘토(Memento)|
||프록시(Proxy)|[옵저버(Observer)](https://github.com/jeonyeohun/Design-Patterns-In-Swift#-%EC%98%B5%EC%A0%80%EB%B2%84-%ED%8C%A8%ED%84%B4-observer-pattern)|
||[데코레이터(Decorator)](https://github.com/jeonyeohun/Design-Patterns-In-Swift#-%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0-%ED%8C%A8%ED%84%B4-decorator-pattern)|스테이트(State)|
|||[전략(Strategy)](https://github.com/jeonyeohun/Design-Patterns-In-Swift#%EF%B8%8F-%EC%A0%84%EB%9E%B5-%ED%8C%A8%ED%84%B4-strategy-pattern)|스테이트(State)||
|||[템플릿 메서드(Template Method)](https://github.com/jeonyeohun/Design-Patterns-In-Swift#-템플릿-메소드-패턴-template-method-pattern)|
|||[반복자(Iterator)](https://github.com/jeonyeohun/Design-Patterns-In-Swift#%EF%B8%8F-반복자-패턴-iterator-pattern)|
|||방문(Visitor)|

<br/>

## 생성 패턴(Creational Pattern)

### 📦 추상 팩토리 패턴 (Abstract Factory Pattern)

추상 팩토리 패턴은 한 팩토리에 여러 연관된 객체를 생성하는 팩토리 메서드를 정의하는 방법입니다. [더 자세히 알아보기](https://jeonyeohun.tistory.com/386)

```swift
import Foundation

protocol View: CustomStringConvertible {
    var id: String { get set }
    var color: String { get set }
}

extension View {
    var description: String {
        return "type: view id: \(id) color \(color)"
    }
}

protocol Button: CustomStringConvertible {
    var id: String { get set }
    var color: String { get set }
}

extension Button {
    var description: String {
        return "type: button id: \(id) color \(color)"
    }
}

class YellowView: View {
    var id: String
    var color: String = "Yellow"
    
    init(id: String) {
        self.id = id
    }
}

class BlackView: View {
    var id: String
    var color: String = "Black"
    
    init(id: String) {
        self.id = id
    }
}

class YellowButton: Button {
    var id: String
    var color: String = "Yellow"
    
    init(id: String) {
        self.id = id
    }
}

class BlackButton: Button {
    var id: String
    var color: String = "Black"
    
    init(id: String) {
        self.id = id
    }
}

protocol ButtonBoxFactory {
    func createView() -> View
    func createButton() -> Button
}

class DarkButtonBoxFactory: ButtonBoxFactory {
    func createView() -> View {
        return BlackView(id: "bv")
    }
    
    func createButton() -> Button {
        return YellowButton(id: "yb")
    }
}

class LightButtonBoxFactory: ButtonBoxFactory {
    func createView() -> View {
        return YellowView(id: "yv")
    }
    
    func createButton() -> Button {
        return BlackButton(id: "bb")
    }
}

class ButtonBox {
    enum ColorTheme {
        case dark, light
    }
    
    private var colorTheme: ColorTheme
    private var buttonBoxFacotry: ButtonBoxFactory
    private var button: Button?
    private var view: View?
    
    init(colorTheme: ColorTheme) {
        self.colorTheme = colorTheme
        self.buttonBoxFacotry = colorTheme == .dark ? DarkButtonBoxFactory() : LightButtonBoxFactory()
        self.createButtonBox()
    }
    
    private func createButtonBox() {
        self.button = buttonBoxFacotry.createButton()
        self.view = buttonBoxFacotry.createView()
    }
    
    func change(colorTheme: ColorTheme) {
        self.colorTheme = colorTheme
        self.buttonBoxFacotry = colorTheme == .dark ? DarkButtonBoxFactory() : LightButtonBoxFactory()
        self.createButtonBox()
    }
    
    func printComponents() {
        print(self.view!)
        print(self.button!)
    }
}

var buttonBox = ButtonBox(colorTheme: .dark)
buttonBox.printComponents()

print("\n## Change Factory ##\n")

buttonBox.change(colorTheme: .light)
buttonBox.printComponents()

// type: view id: bv color Black
// type: button id: yb color Yellow
//
// ## Change Factory ##
//
// type: view id: yv color Yellow
// type: button id: bb color Black
// Program ended with exit code: 0

```

</br>

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
</br>

### ☝️ 싱글톤 패턴 (Singleton Pattern)

싱글톤 패턴은 단 하나의 인스턴스만을 생성하고 추가적인 인스턴스를 생성하지 못하도록하며 코드 전역에서 인스턴스를 공유할 수 있도록 하는 디자인 패턴입니다. [더 자세히 알아보기](https://jeonyeohun.tistory.com/388)

```swift
class Singleton {
    static let shared = Singleton()
    var state = true
    
    private init() {}
}

class A {
    let singleton = Singleton.shared
    
    func printState() {
        print(singleton.state)
    }
    
    func updateState() {
        singleton.state.toggle()
    }
}

class B {
    let singleton = Singleton.shared
    
    func printState() {
        print(singleton.state)
    }
    
    func updateState() {
        singleton.state.toggle()
    }
}

let a = A()
let b = B()

a.printState() // true
b.printState() // true
a.updateState()
a.printState() // false
b.printState() // false
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

### 👇 커맨드 패턴

커맨드 패턴은 객체로 보내는 요청를 캡슐화하여 요청들을 파라미터화 하고 역으로 동작을 수행할 수 있도록 하는 디자인 패턴입니다. [더 자세히 알아보기](https://jeonyeohun.tistory.com/389)

```swift
//
//  main.swift
//  example
//
//  Created by USER on 2022/02/20.
//

import Foundation

final class TVRemoteControl {
    private let onCommand: Commanding
    private let offCommand: Commanding
    private let upCommand: Commanding
    private let downCommand: Commanding
    private var commandHistory: [Commanding] = []
    
    init(
        on: Commanding,
        off: Commanding,
        up: Commanding,
        down: Commanding
    ) {
        self.onCommand = on
        self.offCommand = off
        self.upCommand = up
        self.downCommand = down
    }
    
    func on() {
        self.onCommand.execute()
        self.commandHistory.append(self.onCommand)
    }
    
    func off() {
        self.offCommand.execute()
        self.commandHistory.append(self.offCommand)
    }
    
    func up() {
        self.upCommand.execute()
        self.commandHistory.append(self.upCommand)
    }
    
    func down() {
        self.downCommand.execute()
        self.commandHistory.append(self.downCommand)
    }
    
    func reverseAll() {
        while self.commandHistory.isEmpty == false {
            let command = self.commandHistory.removeLast()
            command.unexecute()
        }
    }
}

final class TV {
    func on() {
        print("on")
    }
    
    func off() {
        print("off")
    }
    
    func up() {
        print("up")
    }
    
    func down() {
        print("down")
    }
}

protocol Commanding {
    func execute()
    func unexecute()
}

final class OnCommand: Commanding {
    let tv: TV
    
    init(tv: TV) {
        self.tv = tv
    }
    
    func execute() {
        tv.on()
    }
    
    func unexecute() {
        tv.off()
    }
}

final class OffCommand: Commanding {
    let tv: TV
    
    init(tv: TV) {
        self.tv = tv
    }
    
    func execute() {
        tv.off()
    }
    
    func unexecute() {
        tv.on()
    }
}

final class UpCommand: Commanding {
    let tv: TV
    
    init(tv: TV) {
        self.tv = tv
    }
    
    func execute() {
        tv.up()
    }
    
    func unexecute() {
        tv.down()
    }
}


final class DownCommand: Commanding {
    let tv: TV
    
    init(tv: TV) {
        self.tv = tv
    }
    
    func execute() {
        tv.down()
    }
    
    func unexecute() {
        tv.up()
    }
}

let appleTV = TV()
let appleRemoteControl = TVRemoteControl(
    on: OnCommand(tv: appleTV),
    off: OffCommand(tv: appleTV),
    up: UpCommand(tv: appleTV),
    down: DownCommand(tv: appleTV)
)

appleRemoteControl.on()
appleRemoteControl.off()
appleRemoteControl.down()
appleRemoteControl.up()
appleRemoteControl.reverseAll()

//on
//off
//down
//up
//down
//up
//on
//off
//Program ended with exit code: 0

```

</br>

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

</br>

### 👀 옵저버 패턴 (Observer Pattern)

옵저버 패턴은 어떤 객체에서 발생하는 이벤트를 해당 객체를 관찰하고 있는 객체에게 알리는 방식으로 메서드를 호출하는 패턴입니다 [더 자세히 알아보기](https://jeonyeohun.tistory.com/215)

```swift
protocol Observable {
    func notify (post: String)
    func add(follower: Follower)
    func remove(follower: Follower)
}
 
class Celebrity: Observable {
    let name: String
    var followers: [Follower] = []
    
    init(name: String) {
        self.name = name
    }
    
    func notify(post: String) {
        for follower in followers {
            follower.update(post: post)
        }
    }
    
    func add(follower: Follower) {
        self.followers.append(follower)
    }
    
    func remove(follower: Follower) {
        guard let removeIndex = followers.firstIndex(where: { $0 === follower }) else { return }
        self.followers.remove(at: removeIndex)
    }
}

protocol Followable {
    func update (post: String)
}
 
class Follower: Followable {
    let name: String
    
    init(name: String) {
        self.name = name
    }
    
    func update(post: String) {
        print("\(name)입니다! -> \(post)")
    }
}

let han = Celebrity(name: "한호열")
han.add(follower: Follower(name: "안준호"))
han.add(follower: Follower(name: "황장수"))
han.add(follower: Follower(name: "조석봉"))

han.notify(post: "호랑이 열정, 호열이에요~")
 
// 안준호입니다! -> 호랑이 열정, 호열이에요~
// 황장수입니다! -> 호랑이 열정, 호열이에요~
// 조석봉입니다! -> 호랑이 열정, 호열이에요~
```

</br>

### 📄 템플릿 메소드 패턴 (Template Method Pattern)

템플릿 메소드 패턴은 알고리즘의 골격을 정의합니다. 템플릿 메소드를 사용하면 알고리즘의 일부 단계를 서브클래스에서 구현해 알고리즘의 단계는 그대로 유지하면서 일부 단계의 내용만 재정의 할 수 있도록 합니다. [더 자세히 알아보기](https://jeonyeohun.tistory.com/391)

```swift
import Foundation

protocol RegistrationSupportable {
    // template method
    func register()

    // steps
    func inputId()
    func validateId()
    func inputPassword()
    func validatePassword()
    func reinputPassword()
    func validateReInputPassword()
    func presentSuccess()
}

extension RegistrationSupportable {
    func register() {
        inputId()
        validateId()
        inputPassword()
        validatePassword()
        reinputPassword()
        validateReInputPassword()
        presentSuccess()
    }

    func inputId() {
        print("input id")
    }

    func inputPassword() {
        print("input password")
    }

    func reinputPassword() {
        print("reinput password")
    }

    func validateReInputPassword(){
        print("password and repassword should be identical")
    }

    func presentSuccess() {
        print("Success!")
    }
}

final class LooseRuleRegistration: RegistrationSupportable {
    func validatePassword() {
        print("password should be more than 1 letter")
    }

    func validateId() {
        print("id should be more than 1 letter and alphanumeric letters are allowed")
    }
}

final class StrongRuleRegistration: RegistrationSupportable {
    func validatePassword() {
        print("password should be more than 8 letters. At leat 2 speical characters should be included")
    }

    func validateId() {
        print("id should be more than 5 letter and only alphabet is allowed")
    }
}

let reg = LooseRuleRegistration()
reg.register()

// input id
// id should be more than 1 letter and alphanumeric letters are allowed
// input password
// password should be more than 1 letter
// reinput password
// password and repassword should be identical
// Success!

let strongReg = StrongRuleRegistration()
strongReg.register()

// input id
// id should be more than 5 letter and only alphabet is allowed
// input password
// password should be more than 8 letters. At leat 2 speical characters should be included
// reinput password
// password and repassword should be identical
// Success!

```

</br>

### ♻️ 반복자 패턴 (Iterator Pattern)

반복자 패턴은 컬렉션의 내부 구현을 노출하지 않으면서 컬렉션의 모든 요소에 접근할 수 있는 방법을 제공합니다. [더 자세히 알아보기](https://jeonyeohun.tistory.com/390)

```swift
protocol Iterable {
    associatedtype Iterator
    func makeIterator() -> Iterator
}

protocol Iterator {
    associatedtype Element
    func hasNext() -> Bool
    func next() -> Element?
}

final class defaultIterator<T>: Iterator {
    typealias Element = T
    private var items: [Element] = []
    private var current = 0

    init(items: [Element]) {
        self.items = items
    }

    func next() -> Element? {
        guard hasNext() else { return nil }
        defer { self.current += 1 }

        return items[current]
    }

    func hasNext() -> Bool {
        current < items.count
    }
}

final class MapCollection: Iterable {
    private var map: [String: String] = [:]

    func add(element: String, for key: String) {
        map.updateValue(element, forKey: key)
    }

    func makeIterator() -> defaultIterator<String> {
        return defaultIterator<String>(items: self.map.values.map({ $0 }))
    }
}

final class ListCollection: Iterable {
    private var list: [String] = []

    func add(element: String) {
        list.append(element)
    }

    func makeIterator() -> defaultIterator<String> {
        return defaultIterator<String>(items: self.list)
    }
}

let map = MapCollection()
let list = ListCollection()

map.add(element: "1", for: "1")
map.add(element: "2", for: "2")
map.add(element: "3", for: "3")

list.add(element: "11")
list.add(element: "22")
list.add(element: "33")

let iterators = [map.makeIterator(), list.makeIterator()]

for iterator in iterators {
    while iterator.hasNext() {
        print(iterator.next() ?? "0")
    }
}

```



