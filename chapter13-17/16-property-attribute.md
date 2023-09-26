# 16. 프로퍼티 어트리뷰트
<br>

## 16.1 내부 슬롯과 내부 매서드
http://ecma-international.org/ecma-262/11.0/#sec-object-internal-methods-and-internal-slots

## 16.3 Property Attributes [(link)]((https://262.ecma-international.org/11.0/#sec-property-attributes))
### 16.3.1 Attributes of a Data Property (데이터 프로퍼티)
|Attribute Name|Attribute Name|Property Descriptor<br>객체의 Property|Discription|
|:--|:--|:--|:--|
|[[Value]]|Any [ECMAScript language type](https://262.ecma-international.org/11.0/#sec-ecmascript-language-types)|value| - property 키를 통해 property 값에 접근하면 반환되는 값.<br> - property 키를 통해 프로퍼티 값을 변경하면 `[[Value]]`에 값을 재할당 한다. 이때 property가 없으면 property를 동적 생성하고 생성된 proprety의 `[[Value]]`에 값을 저장한다.|
|[[Writable]]|Boolean|writable|`false`인 경우 `[[Set]]`을 사용하여 property의 `[[Value]]`를 변경할 수 없는 읽기 전용 property가 된다.|
|[[Enumerable]]|Boolean|enumerable| - property의 열거 가능 여부<br> - `true`인 경우 `for-in`문이나 `Object.keys`` 매서드 등으로 열거할 수 있다.<br>If true, the property will be enumerated by a for-in enumeration ([see 13.7.5](https://262.ecma-international.org/11.0/#sec-for-in-and-for-of-statements)). Otherwise, the property is said to be non-enumerable.|
|[[Configurable]]|Boolean|configurable|- property의 재정의 가능 여부<br> - `flase`인 경우 해당 property의 delete, property attribute 값의 변경이 금지된다.<br> - 단 `[[Writable]]`이 `true`인 경우 `[[Value]]`의 변경과 `[[Writable]]`을 `false`로 변경하는 것이 허용된다.|

### 16.3.2 Attributes of an Accessor Property (접근자 프로퍼티)
|Attribute Name|Value Domain|Property Descriptor<br>객체의 Property|Discription|
|:--|:--|:--|:--|
|[[Get]]|Object \| Undefined|get|접근자 프로퍼티를 통해 프로퍼티의 값을 `읽을 때 호출`되는 `접근자 함수`|
|[[Set]]|Object \| Undefined|set|접근자 프로퍼티를 통해 프로퍼티의 값을 `저장할 때 호출`되는 `접근자 함수`|
|[[Enumerable]]|Boolean|enumerable|상동|
|[[Configurable]]|Boolean|configurable|상동|

> 예제
```js
const person = {
    // Data Property
    firstName: 'Gildong',
    lastName: 'Hong',

    // Accessor Property
    get fullName() {         // getter
        return `${this.firstName} ${this.lastName}`;
    }, 
    set fullName() {         // setter
        [this.firstName, this.lastName] = name.split(' ');
    }
}

// Data Property를 통한 Property Value 참조
console.log(person.firstName + ' ' + person.lastName);

// setter 함수 호출 - Accessor Property를 통한 Property Value 저장
person.fullName = 'Daeha Kim'
console.log(person); // {firstName: "Deaha", lastName: "Kim"}

// getter 함수 호출 - Accessor Property를 통한 Property Value 참조
console.log(person.fullName);  // Daeha Kim

// firstName, lastName은 Data Property이고,
// [[Value]], [[Writable]], [[Enumerable]], [[Configurable]] Attributes를 갖는다.
let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log(descriptor);
// {value: "Gildong", writable: true, enumerable: true, configurable: true}

// fullName은 Accessor Property이고,
// [[Get]], [[Set]], [[Enumerable]], [[Configurable]] Attributes를 갖는다.
let descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log(descriptor);
// {get: f, set: f, enumerable: true, configurable: true}
```
> 내부 슬롯/매서드 관점
> `fullName`으로 property 값이 접근하면 내부적으로 `[[Get]]` 내부 매서드가 호출된다.
> 1. property 키가 유효한지 확인한다.
> 2. prototype 체인에서 property를 검색한다.  (person 객체에 fullName property가 존재)
> 3. 검색된 `fullName`의 property attribute의 종류를 확인한다. (fullName은 Accessor Property)
> 4. `fullName`의 `getter`함수를 호출하여 그 결과를 반환한다.

p.225