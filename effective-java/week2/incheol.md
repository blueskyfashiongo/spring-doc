# 📌 인상 깊었던 내용

## 📚 equals 메서드 부여 조건

> equals 메서드는 동치관계를 구현하며 다음을 만족한다.
> 
> - 반사성 : null이 아닌 모든 참조 값 x에 대해, x.equals(y)는 true다.
> - 대치성 : null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)가 true면 y.equals(x)도 true다.
> - 추이성 : null이 아닌 모든 참조 값 x,y,z에 대해 x.equals(y)가 true이고 y.equals(z)도 true이면 x.equals(z)도 true이다.
> - 일관성 : null이 아닌 모든 참조 값 x,y에 대해 x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.
> - null 아님 : null이 아닌 모든 참조 값 x에 대해 x.equals(null)은 false다.
> 
> 이 규약을 어기면 프로그램이 이상하게 동작하거나 종료될 것이고, 원인이 되는 코드를 찾기도 굉장히 어려울 것이다.
> 
> 📕 54p 15번째 (10장)
>

### **🧐 : 개념을 알고 있으면 좋을것 같아서 참고하자**

# 📌 이해가 가지 않았던 내용

## **📚 Object 외의 타입을 매개변수로 받는 equals 메서드는 선언하지 말자**

> 많은 프로그래머가 equals를 다음과 같이 작성해놓고 문제의 원인을 찾아 헤맨다
> 이 메서드는 Object.equals를 재정의한 게 아니다. 입력 타입이 Object가 아니므로 재정의가 아니라 다중정의한 것이다
> 
> 📕 65p 10번째 (10장)
>

### **🧐 : 간혹 객체 자체를 equals 메서드에 파라미터로 재정의하는 경우가 있는데… 왜 안되는지 잘 모르겠담..ㅜ**

```jsx
public boolean euqlas(Person person) {
	return person.name == this.name;
}
```

# 📌 논의해보고 싶었던 내용

## **📚 hashCode도 재정의하라**

> hashCode를 평소에는 사용할 일이 별로 없을것 같은데 hashMap 동작원리에서 쓰인다
> 
> 📕 67p 5번째 (11장)
>

### **🧐 : hashCode를 평소에 재정의할 일이 없을거라고 생각하는데 나중에 참고하면 좋을것 같아서 적어본다**

```jsx
class Person {
	private String name;
	private Integer age;

	// @Override
	// public int hashCode() {
	// 	return 11;
	// }

	@Override
	public boolean equals(Object obj) {
		Person temp = null;
		if(obj instanceof Person){
			temp = (Person)obj;
		}
		return this.name == temp.name && this.age == temp.age;
	}

	public Person(String name, Integer age) {
		this.name = name;
		this.age = age;
	}
}
```