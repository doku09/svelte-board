# 📕SECTION 1
## $state를 쓰는 이유
스크립트는 단 한번만 실행되기 때문에 한번 할당 된 이후 값이 변해도 ui나 의존성을 들고있는 값이 반응하지 않는다.   
어떤 입력에 대한 반응으로 이름을 변경해야 하는 경우, 우리는 이것을 $state상태로 만들어야한다.

## Signal Based Reactivity
- Sources(state) :source 또는 state 또는 signal이라고도 부른다. 읽기 및 쓰기가 가능한 값이며 종속성이 없습니다.

- Derived: 이펙트라고도 하는 파생된 값과 반응의 소스 역할을 합니다.
그런 다음 다른 소스를 기반으로 순수 함수를 사용하여 계산된 읽기 전용 값인
값을 도출했습니다.

- effect: 컴포넌트가 마운트 될떄와 종속성 중 하나가 변결될때 실행된다.

# 📕SECTION 3
## 🗒️js프록시

프록시 개체를 사용하면 다른 객체에 대한 프록시를 생성하여 해당 객체의 기본 작업을 가로채고 재정의할 수 있음.

 ```
  const proxy = new Proxy(target, handler);
  대상 객체가 프록시하려는 객체(handler)와 가로채려는 객체(handler) 두가지를 인자로 받아 proxy를 생성함
 ```

## 🗒️push() 메서드에서 무한루프가 발생하는 이유

``` javascript
$effect(() => {
    array.push(1) //
}); 
```
배열을 업데이트하면서 동시에 배열에 의존하기 때문에 무한루프가 발생한다. 
같은 참조값의 배열에 요소만 추가하는데 왜 무한루프가 생겨? 

push()는 다음과 같이 동작한다. 
``` javascript
array[array.length] = 1;
array.length++;
```
array.length를 의존하는 동시에 업데이트 한다.


## 🗒️반복문 안에서 상수 정의
`{@const dateObject = new Date(date)}`
``` javascript 
{#each notifications as { title, body, date }}
		{@const dateObject = new Date(date)}
		<li>
			<h5>{title}</h5>
			<p>{body}</p>
            <!-- 변경 전 -->
            <!-- <time datetime={new Date(date).toISOString()}>{new Date(date).toLocaleDateString()}</time> -->
             <!-- 변경 후 ({@const dateObject = new Date(date)} 까지 포함) 반복문에서 태그를 사용해상수를 정의할수있음-->
			<time datetime={dateObject.toISOString()}>{dateObject.toLocaleDateString()}</time>
		</li>
	{:else}
		<p>No notifications</p>
	{/each}

```


## 🆑 svelte5 클래스를 사용하는 이유
클래스는 변수를 캡슐화해서 반응성을 유지할 수 있다. 
아래 코드를 보자
```javascript
class CurrencyConverter {
	baseValue: number | undefined = $state(1);
}

const cc = new CurrencyConverter();
$effect(() => {
	cc.baseValue;
})
```
이렇게 했을때 cc.baseValue가 변경되었을때 반응성이 나타난다. effect는 cc.baseValue를 의존한다. 
### cc는 $state한 상태가 아닌데 어떻게 반응성을 일으킬까

실제 컴파일된 코드를 보면 아래와같다. 바로 클래스에서 컴파일로 get,set을 만들어 $.get를 사용하여 state한 상태를 만들어 반환하기 때문에 반응성을 유지할 수 있다.
```javascript
class CurrencyConverter {
#baseValue = $.state(1);

	get baseValue() {
		return $.get(this.#baseValue);
	}

	set baseValue(value) {
		$.set(this.#baseValue, $.proxy(value));
	}
}
```


- 여러 곳에서 재사용할 수 있는 로직이 필요할때 
- 클래스 내부에 해당 로직을 캡슐화가 필요할때
- 세터와 게터를 사용하는 대신 쓰기 가능한 파생형이 필요한 상황에서도 매우 유용할 수 있습니다.
# 📕SECTION 4
## 💡NOTICE
 - $.get 함수는 객체를 반응형으로 만드는 역할을 한다.
 - 객체 구조분해할당을 사용하면 반응성이 사라진다.

---

# 📺Peter Svelte 
 ## Shallow Reactivity VS Nested Reactivity(중첩된 반응성)

 파생된 신호를 사용해야할때와 사용하지 않아야할때 

 기본적으로 파생된것을 사용할 필요가 없다. 

 복잡한 파생을 사용할때만 derived를 사용? 

## 큐마이크로테스크 
스택에 있는 동기함수를  실행하면 
이벤트 루프가큐마이크로 테스크에 있는 테스크를 실행한다.
$effect는 큐마이크로 테스트에서 실행되므로 동기적인 상태값을 변경하는데 사용되면 부작용이 발생할 수 있다.

`tick()` 은 마이크로 테스크가 전부 실행되는 것을 기다려준다.

```javascript
tick().then(() => {
	console.log({count,double})
})
```

## derived를 언제 쓸것인가 

 ```
 <script>
 let a = $state(0);
 let b = $state(0);
 let c = $state(0);
 let d = $state(0);
 let e = $state(0);

// 1 방법
 function addNumber() {
	return a+b+c+d+e;
 }
// 2 방법
let total = $derived(a + b +c +d +e);
 </script>

<span> {addNumber()} </span>
<span> {total} </span>
 ```
 위의 코드에서 작성한 것은 템플릿 이펙트에 의해 derived를 사용하지 않고 원하는 값을 화면에 표시한다. 

 1 방법은 O(n*m) 성능으로  addNumber()를 호출하는 곳이 많으면 많을 수록 성능은 떨어지지만 연결고리가 분할 되어 있다. 즉 부작용을 일으킬 확률이 적어진다. 

 2 방법은  O(n+m) 성능으로 성능은 낫지만 total 변수 하나에 많은 템플릿이 의존하게 되므로 부작용이 일어날 확률이 생긴다. 

두개의 방법에서는 적절한 방법을 선택해야한다.
> 참고:  [peter svelte](https://www.youtube.com/watch?v=ezW1gc9GqCg)


