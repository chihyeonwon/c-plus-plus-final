# c-plus-plus-final
c-plus-plus-final

# 객체 연산자 

## + 연산자 중복 정의

```c++
#include <iostream>
#include <string>
using namespace std;

class MyVector {
private: 
   double x, y; // 지역변수 x, y 선언
public: 
 MyVector(double x= 0.0, double y= 0.0) : x(x), y(y) { }
 string toString() 
   return"("+to_string(x) +", "+to_string(y) +")";  // to_string은 string 클래스의 멤버함수
 MyVector operator+(const MyVector& v2);
}

MyVector MyVector::operator+(const MyVector& v2);
{
  MyVector v;
  v.x = this->x + v2.x; // this->x : v1의 지역변수, this는 v1
  v.y = this->y + v2.y; // this->y : v1의 지역변수  this는 v1
  return v;
}

int main()
{
  MyVector v1(1.0, 2.0), v2(3.0, 4.0);
  MyVector v3 = v1 + v2; // 복사 생성자 호출 (operator+ 함수에서 매개변수로 객체를 넘겨줌 (return v))
  
  // v1 + v2 <- v1. operator+(v2) // v2가 매개변수로 넘어감
}
```

## ==, != 연산자 중복
```c++
#include <iostream>
using namespace std;

class Time
{
  int hour,min,sec;
public:
  Time(int h= 0, int m= 0, int s = 0) : hour(h), min(m), sec(s) {  }
  
  bool operator== (Time &t2) {
    return (hour == t2.hour && min == t2.min && sec == t2.sec);
  }
  
  bool operator!= (Time &t2) {
    return !(*this == t2);
  }
};

int main()
{
  Time t1(1, 2, 3), t2(1, 2, 3);
  
  cout.setf(cout.boolalpha); // 출력 객체 cout의 멤버함수 setf(setformat) cout.boolalpha : 0, 1이 아니고 true false로 찍어라 
  cout << (t1 == t2) << endl;
  cout << (t1 != t2) << endl;
  return 0;
}
```

## ++ Prefix 전위 연산자 중복

```c++
#include <iostream>
using namespace std;

class Counter {
private:
  int value;
public:
  Counter(): value(0) {};
  ~Counter() { }
  int getValue() const { return value; } // 함수의 반환값이 const
  void setValue(int x) { value =x; }
  Counter& operator++() // 자기가 자기 호출해서 매개변수 없음 & 써주는 이유 : 본객체를 ++ 증가
  {
    ++value; // 지역변수
    return *this; // 복사하기 위해서 생성된 임시 객체가 아니라 본객체를 넘겨주기위해서 // ++(++c) = ++(c.operator++()) = ++(c) 
  }
};

int main()
{
  Counter c;
  cout << "카운터의 값: " << c.getValue() << endl;
  ++c;
  cout << "카운터의 값: " << c.getValue() << endl;
  return 0;
}
```

## ++ postfix 후위 연산자 중복

전위연산자와 후위연산자를 구별하기 위해서 매개변수에 int형을 추가한다.
```c++
const Counter operator++(int i) // int i 매개변수 넘어가는 것도 없고 아무 의미없고 그냥 후위연산자임을 알려주기 위해 넣어줌, const넣어주는 이유 (c++)++ 오류뜨게 하려고, profix는 반환되는 임시객체 ++ 할필요가 없으므로 & 필요없음
{
  Counter temp = {*this}; // 임시 객체 temp에 본객체를 저장해서 보관
  ++value; // 본객체 ++ 증가
  return temp; // 보관된 객체 넘겨줌
}
```

```c++
(v++)++ X
++(v++) O
```

## 대입 연산자 중복

```c++
#include <iostream>
using namespace std;
class Box
{
private:
   double length, width, height;
public:
   Box(double l= 0.0, double w = 0.0, double h = 0.0) : length(l), width(w), height(h) { }
   void display() {
      cout << "(" << length << ", " << width << ", " << height << ")" << endl;
   }
   
   Box& operator=(const Box& b2)
   {
      this->length = b2.length;
      this->width = b2.width;
      this->height = b2.height;
      return this;
   }
};

int main()
{
   Box b1(30.0, 30.0, 60.0), b2;
   b1.display();
   
   b2 = b1; // b2.operator=(b1)
   b2.display():;
   
   return 0;
}

대입 연산자는 참조자를 반환해야 한다.   
그 이유는 대입 연산자는 연속하여 적용될 수 있기 때문이다.   
대입 연산자와 복사 생성자는 많이 혼동되는 문제이다.   

```c++
MyArray first; // 기본 생성자 호출
MyArray second(first); // 복사 생성자 호출
MyArray third = first; // 복사 생성자 호출
MyArray third {first}; // 복사 생성자 호출

second = third; // 대입 연산자 호출
```

## 인덱스 연산자 [ ]의 중복

```c++
int A[10];
A[-1] = 0; // 오류
```

인덱스 연산자의 중복 -> 인덱스의 범위를 체크

```c++
int &operator[] (int i) { // 형식이 아니라 인덱스 번호, &를 넣어주는 이유 : 자기 자신 객체 A[3] 이게 없으면 복사되서 
   if(i >= SIZE) {
      cout << "잘못된 인덱스: ";
      return a[0];
   }
   else if(i < 0) // 0보다 작은 음수라면
   {
      cout << "잘못된 인덱스: ";
      return a[0];
   }
   return a[i];
}

int main() {
   MyArray A;
   
   A[3] = 9; // 인덱스 연산자 함수 호출 A[3].operator[](3) = 9; return a[3] = 9
   cout << "A[3]=" << A[3] << endl;
   cout << "A[16]=" << A[16] << endl;
   
   return 0;
}
```

## 포인터 연산자의 중복

## 상속(inheritance)

class Car
{
   int speed; // 속성값
};

class SportsCar: public Car
{
   bool turbo; // 속성값
};

SportsCar 클래스는 Car 클래스를 상속한다.

Class Diagram (UML) 그림으로 표현   
- : private member
+ : public member

```c++
#include <iostream>
#include <string>
using namespace std;

class Car {
private:
   int speed; // 속도
public:
   void setSpeed(int s) { speed = s; }
   int getSpeed() { return speed; }
};

class SportsCar : public Car {
private:
   bool turbo;
public:
   void setTurbo(bool newValue) { turbo = newValue; }
   bool getTurbo() { return turbo; }
};

int main()
{
   SportsCar c; // 부모클래스 메모리 먼저 잡히고, 자식클래스 메모리 잡힌다.
   
   c.setSpeed(60); // 부모클래스 Car 함수 호출
   c.setTurbo(true); // 자기자신 자식클래스 함수 호출
   c.setSpeed(100);
   c.setTurbo(false);
   return 0;
}

자식클래스는 부모클래스의 속성값을 사용할 수 있다. (public)

상속은 왜 필요한가?   
-> 공통되는 속성을 뽑아서 상위 클래스(부모 클래스)를 만드는 일반화(generalization)을 거친다.(상속개념)   
<-> 반대로 공통된 클래스로 하위클래스(자식 클래스)를 만드는 것(specialization)   
상속은 여러 단계로 이루어질 수 있다.   
상속은 is-a kind of 관계이다.   
ex) 자동차는 탈 것의 종류 중 한 개이다.(일반화)   
has-a (composed of) ~로 구성되어 있다. (집합 관계)는 상속으로 모델링을 하면 안 된다.   
library has a book.   

parent=base=super class 상위      
child=derived=sub class 하위   

## 상속과 생성자/소멸자

객체가 생성될 때 자식 클래스 생성자 뿐만 아니라 부모클래스의 생성자도 호출된다.   
장유유서 개념 -> 부모 클래스 생성자 호출 먼저 되고 자식 클래스 생성자가 호출된다.   
소멸될 때는 자식클래스가 먼저 소멸되고 그 다음 부모 클래스가 소멸된다.   

```c++
#include <iostream>
#include <string>
using namespace std;

class Shape {
private:
   int x, y;
public:
   Shape() { cout << "SHAPE 생성자" << endl; }
   ~Shape() { cout << "SHAPE 소멸자" << endl; }
};

class Rectangle : public Shape {
private:
   int width, height;
public:
   Rectangle() { cout << "RECTANGLE 생성자" << endl; }
   ~Rectangle() { cout << "RECTANGLE 소멸자" << endl; }
};

int main()
{
   Rectangle r; // Rectangle 객체 r 생성
   return 0; // 생성된 객체가 소멸되어야 한다.
}

```
출력결과
```c++
SHAPE 생성자   
RECTANGLE 생성자
RECTANGLE 소멸자
SHAPE 소멸자
```

## 부모 클래스의 생성자를 지정하는 방법

부모 클래스의 생성자가 default 생성자, 매개변수 초기화하는 생성자 등 여러 개가 있을 수 있다.  
이때 부모 클래스의 생성자를 지정할 수 있는 방법이 있다.   

```c++
자식 클래스의 생성자() : 부모클래스의 생성자()
{
}
```
예제
```c++
Rectangle (int x=0, int y=0, int w=0, int h=0) : Shape(x, y)
{
   width = w;
   height = h;
}
뒤에 오는 부모클래스의 생성자는 값을 호출하는 형태로 지정해야 한다. Shape(int x, int y) X

## 접근 지정자

protected 지정자는 상속받는 클래스에서 사용한다.   
외부에서는 사용x(전용멤버)이지만 자식 클래스는 protected 사용 가능

부모 클래스에 protected를 지정해주면 자식 클래스에서만 사용 가능, 외부에서는 사용X

## 멤버 함수 재정의

polymophism 함수 다형성   

1. 함수 중복정의(overloading)   
2. 함수 재정의(overriding) 상속에서 사용   

부모클래스의 정의되어 있는 함수를 자식클래스에 맞게끔 자식클래스에서 정의하는 것을 함수 재정의(overriding)이라고 한다.   

같은 클래스 내에서 함수 정의 -> 중복정의   
상속 관계 클래스에서 함수 정의 -> 재정의   

## 부모클래스의 함수를 호출하고 싶다.

```c++
void print() { // print함수 재정의를 했지만
   ParentClass::print(); 부모 클래스의 print를 호출하고 싶을 때 다음과 같이 사용한다.
```   

## 상속 받는 경우

private로 상속 -> 부모클래스 속성 접근 x      
public 로 상속 -> public으로 상속      
protected로 상속 -> protected으로 상속      

일반적으로 public으로 쓰지만 좀 더 수준이 높은 보안이 필요할 때는 protected를 사용한다.   
상속 받을 때 접근 지정자를 줄 수 있다.
```c++
class Derived : private Base // public, protected 속성값들을 상속을 받지만 자식 클래스에서 private가 된다.
```


