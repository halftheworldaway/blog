## 1.为什么要用const
const本质上其实不仅仅是对变量的一个限定符，更是对程序员的一个限定符，它提醒了程序员，通过const限定的变量的值是不应该被更改的，并且当编译器得知变量或者表达式的求值结果是恒定的时候，编译器也可以在编译期大展拳脚，为我们做出一些编译期优化，进而提高我们的程序在运行时的性能（例如编译器会在编译时直接将字面值常量插入我们的代码段)。
## 2.const的基本用法
- 用来进行基本类型变量的声明
    ```cpp
    /*const变量在初始化的时候必须赋予值*/
    int z;//正确: 常规变量初始化可不给明确值
    const int x = 5; //正确: 以字面值常量5来进行初始化
    //const int y;  错误：const变量的声明必须进行初始化
    const int y = z; //正确：const型的变量可以被非const型变量进行初始化

    /*const变量不允许被修改*/
    //x = 3; y = 3 //错误：不允许对const型变量进行修改
    ```
- 用来对指针或者引用这种复合类型进行声明
    ```cpp
    /*关于常量指针与指针常量*/
    int value = 5;
    const int c_value = 5;
    const int* p = &value; //正确：pointer to const 可以被常规变量地址初始化
    const int* p1 = &c_value; //正确：pointer to const 可以被const变量地址初始化
    //*p = 3, *p1 = 3 错误：无法通过pointer to const来改变所指向变量的值

    int* const p2 = &value; //正确: 声明一个const pointer
    *p2 = 3; //正确: 可以通过const pointer来更改它所指向的值
    //p2 = &c_value; 错误: const pointer所指向的地址无法被修改

    /*引用类型与指针类型类似*/
    const int& cref = value; //正确: 可通过常规变量初始化
    const int& cref_1 = c_value;//正确: 可以通过const变量初始化
    //cref = 4 错误: 不可通过reference to const改变原变量的值
    ```
    其实在c++primer中，对于const修饰指针的这种情况有一个非常好的说明：
    >指针本身是一个对象，它又可以指向另一个对象。因此，指针本身是不是常量以及指针所指的是不是一个常量就是两个相互独立的问题。用名词顶层const表示指针本身是一个常量，而用名词底层const表示指针所指的对象是一个常量。
- 用于面向对象程序相关设计时
    ```c++
    class base {
        private:
            int m_data;
            mutable int m_data_1;
        public:
            void print() const {cout << "Const version!\n";} //声明一个const型的函数
            void print() {cout << "Non const version\n";} //同名非const型函数，可以与上面函数构成重载
            /* void set(int x) const {m_data = x;} 错误：不能在const成员函数中改变m_data的值 */
            void set(int x) const {m_data_1 = x;} //正确:可以对mutable变量进行修改
    }
    //并且当实例化一个const base对象时，其只能调用其常成员函数。
    ```

## 3.总结
在初始化时，const型变量往往没有特别严格的要求，无论用const变量，字面值常量还是常规变量来进行初始化都是可以的操作，但是一旦初始化完成后，const变量便开始拒绝修改操作，并且我们可以将这个性质推广到类的成员函数之上，类的常成员函数不可以对内部成员变量进行修改（大部分情况下），而至于通过类实例化的常对象也只能调用我们的常成员函数。总的来说，const可以让我们的程序更加安全，通过这种编译时的检查，我们可以确保一些我们不想变更的值保持其原本的样子。
