# 16. 프로퍼티 어트리뷰트
<br>

공부하면서 왜 이런 프로퍼티 어트리뷰트를 공부해야 할까에 대해서 생각해보고 알아본 바에 의하면,<br>
좀 더 고급스럽고, 세밀한 설정을 가능하게 해주는 부분인것 같다.
어떤 사물을 원리를 모르고 사용할 줄 아는것과 원리를 알고 사용하는 것에는 시간이 지날 수록 많은 차이를 만들어 낸다. 이 부분도 안배우고 넘어가도 7~80% 정도의 코딩은 할 수 있을 것으로 보이나, 좀 더 깊은 코딩을 위해서는 반드시 알아두어야 할 항목이라고 생각된다.

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

## 16.4 프로퍼티 정의
`Object.defineProperty`매서드를 사용하여 정의한다.
```js
const person = {};
Object.defineProperty(person, 'firstName', {
    value: 'Ungmo',
    writable: true,
    enumerable: true,
    configurable: true,
});
Object.defineProperty(person, 'lastName', {
    value: 'Lee', 
});   // 설정하지 않은 writable, enumerable, configurable 모두 기본값인 false
```
> `lastName` 프로퍼티 <br>
> 1. `[[writable]]` : `fales` - `[[value]]`의 값을 변경할 수 없다.
> 2. `[[enumerable]]` : `false` - `for...in` 문이나 `Object.keys` 등으로 열거할 수 없다.
> 3. `[[configurable]]` : `false` - 해당 프로퍼티를 삭제할 수 없다.
> 4. `[[configurable]]` : `false` - 해당 프로퍼티를 재정의할 수 없다.

## 16.5 객체 변경 방지
|구분                              |매서드                    |추가|삭제|읽기|쓰기|재정의|
|:---------------------------------|:--                     |:--:|:--:|:--:|:--:|:--:|
|[객체 확장 금지](#1651-객체-확장-금지)|Object.preventExtensions|×|○|○|○|○|
|[객체 밀봉](#1652-객체-밀봉)         |Object.seal             |×|×|○|○|×|
|[객체 동결](#1653-객체-동결)         |Object.freeze           |×|×|○|×|×|

### 16.5.1 객체 확장 금지
확장이 금지된 객체는 프로퍼티 추가가 금지된다.
```js
const person = { name: 'Lee' }
console.log(Object.isExtensible(person));  // 확장이 금지된 객체가 아님 : true

// 확장을 금지
Object.preventExtensions(person);
console.log(Object.isExtensible(person));  // 확장이 금지된 객체가 아님 : false
```

### 16.5.2 객체 밀봉
밀봉된 객체는 읽기와 쓰기만 가능하다.
```js
console.log(Object.isSealed(person));  // false
Object.seal(person);
console.log(Object.isSealed(person));  // true
```

### 16.5.3 객체 동결
동결된 객체는 읽기만 가능하다.
```js
console.log(Object.isFreeze(person));  // false
Object.freeze(person);
console.log(Object.isFreeze(person));  // true
```

### 16.5.4 불변 객체
위의 3가지 변경 방지 매서드는 얕은 변경 방지이므로, 직속 프로퍼티만 변경이 방지되고, 중첩 객체까지 영향을 주지 못한다.
```js
const person = {
    name: 'Lee',
    address: { city: 'Seoul' }
}
// 얕은 객체 동결
Object.freeze(person);
// 직속 프로퍼티만 동결됨
console.log(Object.isFrozen(person));            // true   변경 불가
console.log(Object.isFrozen(person.address));    // false  변경 가능
```

따라서 객체의 중첩 객체까지 동결하여 변경이 불가능하게 하려면, 재귀적으로 동결해야 한다.
```js
function deepFreeze(target) {
    if (target && typeof target === `object` && !Object.isFrozen(target)) {
        Object.freeze(target);
    }
    Object.keys(target).forEach(key => deepFreeze(target[key]));
}
return target;
deepFreeze(person);
```