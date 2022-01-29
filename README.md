# 스위프트로 이해하는 디자인 패턴 👨🏻‍🎨

GoF 디자인 패턴을 스위프트로 구현해가며 정리하는 저장소입니다.

## Table of Contents 

|생성 패턴|구조 패턴|행위 패턴|
|:-:|:-:|:-:|
|추상 팩토리(Abstract Factory)|어댑터(Adapter)|책임 연쇄(Chain of Responsibility)|
|빌더(Builder)|브릿지(Bridge)|커맨드(Command)|
|팩토리 메서드(Factory Methods)|컴포지트(Composite)|인터프리터(Interpreter)|
|프로토타입(Prototype)|퍼사드(Facade)|미디에이터(Mediator)|
|싱글톤(Singleton)|플라이웨이트(Flyweight)|메멘토(Memento)|
||프록시(Proxy)|옵저버(Observer)|
|||스테이트(State)|
|||전략(Strategy)|
|||템플릿 메서드(Template Method)|
|||방문(Visitor)|


## 생성 패턴(Creational Pattern)

### 🏭 팩토리 메서드 패턴 (Factory Method Pattern)

팩토리 메서드 패턴은 객체의 인스턴스를 생성하는 인터페이스를 제공하고, 인스턴스의 생성은 서브클래스에서 정의하도록 하는 방법입니다. 따라서 인스턴스가 생성될 때는 입력에 따른 적절한 팩토리 객체를 선택해 해당 객체를 통해 인스턴스를 생성하게 됩니다.

```swift
import Foundation

protocol Phone {
    func ring()
}

final class IPhone: Phone {
    func ring() {
        print("ring ring from iphone!")
    }
}

final class Galaxy: Phone {
    func ring() {
        print("ring ring from galaxy!")
    }
}

enum PhoneType {
    case iPhone, galaxy
}

final class PhoneFactory {
    func makePhone(type: PhoneType) -> Phone {
        switch type {
        case .iPhone:
            return IPhone()
        case .galaxy:
            return Galaxy()
        }
    }
}

let factory = PhoneFactory()
factory.makePhone(type: .galaxy).ring()
factory.makePhone(type: .iPhone).ring()

```
