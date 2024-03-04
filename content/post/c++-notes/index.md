---
title: "C++ notes"
date: 2023-12-25T20:00:00+08:00
draft: false
math: true
---

這篇文章主要是給自己看的，因此寫的方法是以自己看懂為主，一些自己已經會的東西就不會寫得太詳細。

## Source Code to Machine Code

source code to machine code 分成四個步驟：
1. preprocessing
    - 處理 #，例如 #include
    - 把 header file 複製到當前的檔案裡面
2. compiling
    - turn code into assembly
3. assembling
    - turn assembly into machine code, 0s and 1s
4. linking
    - 把不同檔案的 machine code 接在一起

雖然說有四個步驟，但是其實會把這四個步驟叫做 compiling。

## Command Line Arguments

```c++
int main(int argc, char *argv[]){ 
}

int main(int argc, char **argv){
}
```

如果有一個 binary file 叫做 greet，並且在 terminal 輸入 `./greet Hello`，此時 `argc = 2, argv[0] = "./greet", argv[1] = "Hello"`。

`echo $?` 會回傳來自 main funciton 的 int，也就是 error code。

## The Cherno C++ series

### static
https://youtu.be/f3FVU-iwNuA  
https://youtu.be/V-BFlMrBtqQ
```c++
// static.cpp
static int s_variable = 1;;

// main.cpp
#include <iostream>

int s_variable = 3;

int main(){
    std::cout << s_variable << std::endl; // 3
    return 0;
}
```
static.cpp 中的 s_variable 只能被 static.cpp 中的東西看到，所以並不會有 linking error。  
在命名 global variable 的時候要很小心，因為一旦命名，其他所有的檔案就不能有此變數，不然就要加上 static。

而 static 在 class 中則像是 class 的全域變數。
```c++
#include <iostream>

class Entity{
public:
    static int x;
};

int Entity::x;

int main(){
    Entity e;
    e.x = 1;
    std::cout << e.x << std::endl;

    Entity e2;
    e2.x = 3; // e.x 也變成 3
    std::cout << e.x << std::endl;
    return 0;
}
```
這些 static variable（或是 static function）是跟 class 綁在一起的，而不是 object。因此實務上要寫的話改成以下的方式會比較好，也比較直觀。
```c++
#include <iostream>

class Entity{
public:
    static int x;
    static void print(){
        std::cout << x << std::endl;
    }
};

int Entity::x;

int main(){
    Entity e; // 甚至可以不需要這一行，因為 static member 跟 object 無關
    Entity::x = 1; // 不要寫 e.x = 1
    Entity::print(); // 不要寫 e.print()
    return 0;
}
```
最後，static function 只能存取 static variables，畢竟 static 只跟 class 有關，跟 object 無關，要特別注意。
## OOP

### Encapsulation 封裝
把 data members 透過 member functions 來存取。

```c++
class person {
private:
    int Weight;
    int Height;
public:
    int get_Weight(){
        return Weight;
    }
    int get_Height(){
        return Height;
    }
    void set_Weight(int weight){
        Weight = weight;
    }
    void set_Height(int height){
        Height = height;
    }
};
```

在上面的例子中，為什麼不直接透過 `person.Weight` 來修改數值？  
假設這個數值必須要符合一定的條件，例如：體重必須大於 0，那麼可以這樣寫
```c++
void set_Weight(int weight){
    if (weight > 0){
        Weight = weight;
    }
}
```
來達到我們要的目的。  
而直接修改 `person.Weight`，可能會修改成不符合條件的數值。

### Abstraction 抽象化

在使用程式的時候，讓使用者不必關心程式本身是如何執行的，只要讓使用者知道如何使用即可。  
例如 C++ 的 `lower_bound, pow`，我們不必知道他的原理，只要知道如何使用就好。

### Inheritance 繼承
#### private, protected, public members
```c++
#include <iostream>

class base {
private:
    int pvt = 1;
public:
    int get_private(){
        return pvt;
    }
};

class derived: public base {
};

int main (){
    derived d;
    std::cout << d.get_private();
    return 0;
}
```
上面這段程式碼可以正常運作，但是如果改成
```c++
#include <iostream>

class base {
private:
    int pvt = 1;
public:
    int get_private(){
        return pvt;
    }
};

class derived: public base {
};

int main (){
    derived d;
    std::cout << d.pvt; // 改了這一行
    return 0;
}
```
就會出現 `member "base::pvt" (declared at line 5) is inaccessible`。  
原因就是 `pvt` 是 base class 的 private member，因此只能在 base class 中存取。若是要讓 `pvt` 在 derived class 也能被存取，要改成
```c++
class base {
protected: // pivate 改成 protected
    int pct = 1;
public:
    int get_protected(){
        return pct;
    }
};
```
protected 跟 private 的差異在於 derived class 能不能存取。protected 可以，而 private 不行。

#### private, protected, public inheritance
接下來是 public, protected, private inheratence。  
如果使用 `class derived: public base{};`，那麼在 base class 中的 public member 在 derived class 還會是 public member，在 base class 中的 protected member 在 derived class 還會是 protected member。  
如果使用 `class derived: protected base{};`，那麼在 base class 中的 public member 以及 protected member 在 derived class 都會變成 protected member。  
如果使用 `class derived: private base{};`，那麼在 base class 中的 所有 member 在 derived class 都會變成 private member。  

更詳細的說明可以看這個網站 https://www.programiz.com/cpp-programming/public-protected-private-inheritance

#### inheriting constructors

如果 base class 有 constructor 的話，那麼在 derived class 就要寫一個 constructor，並不會有 default constructor。

```c++
class base {
private:
    int member;
public:
    base(int Member){
        member = Member;
    }
};

class derived: public base {
public:
    derived(int Member): base(Member){
    }
};
```

在 C++11 中，可以使用 `using` 來繼承 constructor，這會繼承所有的 base class constructor。

```c++
class base {
private:
    int member;
public:
    base(int Member){
        member = Member;
    }
};

class derived: public base {
    using base::base;
};
```

### Polymorphism 多型

利用 base class 來對 derived class 進行操作。  
好處在於管理物件的時候，可以直接用共同的 base class 來管理，而不需要用個別的 derived class 一個一個處理。  
要注意的是，只能使用 base class 有的 member funcitons 來操作（如果 base class 跟 derived class 有名稱一樣的 function，也只會執行 base class 的 fucntion），如果想要在 derived class 進行 override，要在 base class 的 function 前面加上 `virtual`，並且可以在 derived class 的 function 後面加上 `override` 來增加可讀性以及方便除錯。  

如果 base class 有 pure virtual function 的話，會變成 abstract class（或是叫做 interface），沒辦法創造出此 class 的 object。而且 derived class 一定要 override，不然 derived class 也會變成 abstract class，可以藉此來強制derived class 進行 override。  

如果想要執行只在 derived class 的 function，可以使用 dynamic cast 來運行。

```c++
#include <iostream>

class base {
public:
    virtual void info(){
        std::cout << "base class object" << std::endl;
    }
    // or we can write "virtual void info() = 0;"
    // set it to pure virtual function
};

class derived: public base {
public:
    void info() override { // void info() override 來增加可讀性以及方便除錯
        std::cout << "derived class object" << std::endl;
    }
};

int main (){
    derived d;
    base *ptr = &d;
    ptr->info(); // derived class object

    base b;
    ptr = &b;
    ptr->info(); // base class object
    
    return 0;
}
```

## 參考資料
1. https://openhome.cc/Gossip/CppGossip/  
2. https://youtu.be/wN0x9eZLix4  
3. https://www.programiz.com/cpp-programming/public-protected-private-inheritance
4. The Cherno C++ series https://www.youtube.com/@TheCherno