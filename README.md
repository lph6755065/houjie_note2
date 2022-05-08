# houjie_note_2
## C++ 11-14

## template<typename T, typename... types>
可变参模板定义了第一个参数，然后其余参数可以不用确定数量和类型直接打包放在types里面  

```cpp
#include <iostream>
#include <bitset>
using namespace std;
void print(){} //递归函数的出口
template<typename T, typename... types>
void print(const T& firArg, const types&... args){
    cout<< firArg <<endl;
    //cout << sizeof...(args);
    print(args...); // 调用其余参数
}
template<typename... types>
void print(const types&... args){
   
   
}
int main()
{
    print(7.5,"hello", bitset<8>(233),42);

    return 0;
}
```
结果为：
```cpp
7.5
hello
11101001
42

```

```cpp
#include <iostream>
#include <tuple>
using namespace std;

template<typename... Values> class tuple;
template<> class tuple<>{};
template<typename Head, typename... Tail>
class tuple<Head,Tail...>:private tuple<Tail...>
{
    typedef tuple<Tail...> inherited;
    public:
    tuple(){}
    tuple(Head v, Tail... vTail):m_head(v), inherited(vTail...){}
    typename Head::type head(){
        return m_head;
    }
    inherited& tail(){
        return *this;
    }
    protected:
    Head m_head;
}
int main()
{
    tuple<int, float, string> t;
    t(41,6.3,"nico");
    t.Head();
    t.tail();
    t.tail().head();
    

    return 0;
}
```

## initializer_list 

这个的本质是array容器

初始化列表不会限制参数的个数，但是参数的类型必须一致，
```cpp
#include <iostream>

using namespace std;

int i{};
int main()
{
   cout<<i;  //默认为0

    return 0;
}
```

```cpp
#include <iostream>
//#include <initializer_list>
using namespace std;

void print(initializer_list<int> vals){
    for (auto p = vals.begin(); p!= vals.end(); ++p){
        cout<< *p << endl;
    }
}
int main()
{
    print({12,3,5,5,6,8});

    return 0;
}
```

```cpp
#include <iostream>
#include <initializer_list>
using namespace std;
class P{
    public:
    P(int a, int b) {
        cout << "p(int, int),a = " << a <<", b = " << b << endl;
    }
    P(initializer_list<int> init){
        cout <<"p(init,valus = ";
        for (auto i : init)
        cout << i << ' ';
        cout << endl;
    }
};
int main()
{
    P p(5,6);
    P q{5,6};
    P r{2,3,4};
    P s = {77, 5};
    return 0;
}
```
如果去掉P的初始化列表形式，这样写的话也是对的，但是必须参数个数一致。
```cpp
#include <iostream>
#include <initializer_list>
using namespace std;
class P{
    public:
    P(int a, int b) {
        cout << "p(int, int),a = " << a <<", b = " << b << endl;
    }
   /* P(initializer_list<int> init){
        cout <<"p(init,valus = ";
        for (auto i : init)
        cout << i << ' ';
        cout << endl;
    }*/
};
int main()
{
    P p(5,6);
    P q{5,6};
    //P r{2,3,4};
    P s = {77, 5};
    return 0;
}
```
结果为：
```cpp
p(int, int),a = 5, b = 6
p(int, int),a = 5, b = 6
p(int, int),a = 77, b = 5
```
初始化列表在C++11之后基本都内置了.比如vector等等。

下面是内置的比较大小，比较起来不会限制比较的数量多少。
```cpp

#include <iostream>
//#include <initializer_list>
#include <algorithm>
using namespace std;

int main()
{
    cout << max({"233","23","33","44"}) << endl;
    cout << min({"233","23","33","44"}) << endl;
    cout << max({"ab","abc","ace"}) << endl;
    cout << min({"ab","abc","ace"}) << endl;
    cout << max({233,23,33,44}) << endl;
    cout << min({233,23,33,44}) << endl;
    
    return 0;
}
```  
结果为：
```cpp
44
233
ace
ab
233
23
```
## explict 

运用explict的写法
```cpp
#include <iostream>
//#include <initializer_list>
#include <algorithm>
using namespace std;
struct Complex{
    int real, imag;
    
    
    explicit Complex(int re, int im = 0):real(re), imag(im){}
    Complex operator+(const Complex& x){
        return Complex((real + x.real),(imag + x.imag));
    }
};
int main()
{
  
    Complex c1(12,5);
    Complex c2 = c1 + 5;
    cout << c1.real << " " << c1.imag<< endl;
    cout << c2.real << " " << c2.imag << endl;
    
    return 0;
}

```  
结果为：
```cpp

error: no match for ‘operator+’ (operand types are ‘Complex’ and ‘int’)
```
如果去掉了explict就可以直接让复数的实部相加了.这种叫做non-explict one argument 只有这种才可以Complex c2 = c1 + 5;
```cpp
#include <iostream>
//#include <initializer_list>
#include <algorithm>
using namespace std;
struct Complex{
    int real, imag;
    
    
    Complex(int re, int im = 0):real(re), imag(im){}
    Complex operator+(const Complex& x){
        return Complex((real + x.real),(imag + x.imag));
    }
};
int main()
{
  
    Complex c1(12,5);
    Complex c2 = c1 + 5;
    cout << c1.real << " " << c1.imag<< endl;
    cout << c2.real << " " << c2.imag << endl;
    
    return 0;
}
```
结果为：
```cpp
12 5
17 5
```


稍微完整的复数的写法
```cpp
#include <iostream>
//#include <initializer_list>
#include <algorithm>
using namespace std;
struct Complex{
    int real, imag;
    
    Complex(int re, int im):real(re), imag(im){}
    Complex operator+(const Complex& x){
        return Complex((real + x.real),(imag + x.imag));
    }
};
int main()
{
  
    Complex c1(12,5);
    Complex c2 = c1; //(17, 5)
    c2.real = c1.real + 5;
    c2.imag = c1.imag + 5;
    cout << c1.real << " " << c1.imag<< endl;
    cout << c2.real << " " << c2.imag << endl;
    
    return 0;
}
```

结果为：
```cpp
12 5
17 10
```
