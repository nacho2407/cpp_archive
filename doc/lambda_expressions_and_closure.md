# Lambda Expressions & Closure

&nbsp;간단하게 **익명 함수**를 생성하는 방법. 특히 범위 기반 for-loop와 함께 사용되어 매우 유용하게 사용할 수 있다. 함수의 내용과 그 함수가 정의된 환경을 함께 묶은 개념을 **클로저**라고 하는데, 함수가 정의되는 시점의 환경을 기억하고 그 환경에서 외부 변수에 접근할 수 있도록 한다. 람다 함수는 다음과 같이 정의한다.

```C++
[introducer_capture] (parameters) -> return_type { statements; }
```

&nbsp;parameters와 return_type은 없다면 생략이 가능하나, **`[]`는 생략할 수 없다**.


&nbsp;람다 함수를 사용할 때 단순히 인자로 값을 넘겨서 함수 내부에서 사용할 수도 있지만, `std::for_each`등 Standard Libraries와 함께 사용하는 경우와 같이 인자를 마음대로 설정할 수 없는 경우가 있는데, 이 때 외부의 변수를 함수 내부에서 사용할 수 있도록 introducer_capture에 선언할 수 있다.


&nbsp;`[&] {}`와 같이 작성하여 외부의 모든 변수를 레퍼런스로 캡처하거나 `[=] {}`와 같이 작성하여 외부의 모든 변수를 값으로 캡처할 수 있다. 물론 `[=, &x, &y] {}`와 같이 지정한 방식으로 캡처할 수도 있다. **캡처되는 내용은 컴파일 기준이 아닌 해당 클로저 객체가 생성되는 시점을 기준으로 한다**. 값으로 캡처되는 변수가 클래스 형이라면 해당 클래스의 (복사) 생성자 또한 클로저 객체가 생성되는 시점에 호출된다.


&nbsp;주의할 점은 **값으로 캡처된 변수들은 constant로 취급**되기 때문에 캡처된 변수의 값을 바꿀 수 없는데, **`[=] mutable {}`와 같이 `mutable` 키워드를 사용하면 캡처된 값들이 함수 내의 지역 변수처럼 취급**된다.


&nbsp;람다 함수를 이용한 클로저 객체는 설계 의도 상 **어떤 형태의 함수가 사용될 지 바로 예측할 수 없기 때문**에, 실제로 함수의 인자 등으로 사용하려면 **템플릿이나 `auto`로 사용**할 수 있다. 함수 포인터를 이용해서 저장할 수도 있는데, 이 경우 외부 변수를 캡처하는 람다 함수는 저장할 수 없다는 단점이 있었다. 대신 **C++ 11부터는 `<functional>`의 `std::function`을 통해서 쉽게 클로저 객체를 저장하고 접근**할 수 있다. `std::function`은 아래와 같이 사용한다.

```C++
std::function<return_type(parameters)> func = [introducer_capture] (parameters) -> return_type { statements; }
```
<br>

&nbsp;**람다 함수를 이용하여 재귀 함수를 작성**할 때도 `std::function`이 도움을 준다. `std::function`을 사용하지 않고 람다 함수를 재귀적인 형태로 만들 때는 **호출 시점에 해당 람다 함수 내에서 재귀 호출되는 함수가 어떤 형태인지를 파악할 수 없어서** 자기 자신을 인자로 넘겨주는 식의 형태로 구현했어야 했다.

```C++
auto func = [](auto self)
{
        // ...

        return self(self);
}

// 호출 시,
func(func);
```

&nbsp;하지만 `std::function`을 이용하면 람다 함수 정의 전에 함수의 형태를 미리 선언할 수 있기 때문에 더 유연한 람다 함수를 작성할 수 있다.

```C++
std::function<void(void)> func;
func = []
{
        // ...

        return func;
}
```
