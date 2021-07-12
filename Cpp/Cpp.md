#  C++学习笔记

##  C++内存管理

内存管理目的：

1. 提高速度
2. 减小内存浪费

<img src=".\img\image-20210712140141274.png" alt="image-20210712140141274" style="zoom:50%;" />

###  primitives(基本工具)

<img src=".\img\image-20210712140647295.png" alt="image-20210712140647295" style="zoom:50%;" />

####  基本工具的使用

```C++
/*
 * C++内存管理的几种形式
 */

// malloc && free
void *p1 = malloc(512);  // 512bytes
free(p1);

// new && delete
complex<int> *p2 = new complex<int>
delete p2;

// new() && delete()
void *p3 = ::operator new(512);  // 512bytes
::operator delete(p3);

// 以下使用C++标准库提供的allocators.
// 其接口虽有标准规范，但实现厂商并未未完全遵守

// VC
#ifdef _MSC_VER
    // 以下两函数都是non-static,定要通过object调用，以下分配3个int
    // 对象allocator<int>()生命周期仅在那一行语句中
    int *p4 = allocator<int>().allocate(3,(int *)0);
    allocator<int>().deallocate(p4,3);
#endif
// Borland C
#ifdef __BORLANDC__
    // 以下两函数都是non-static,定要通过object调用，以下分配5个int
    int *p4 = allocator<int>().allocate(5);
    allocator<int>().deallocate(p4,3);
#endif 
// GUNC 2.9版本以下，以上版本同上
#ifdef __GUNC__
    // 以下两函数都为static，可通过全名调用之。以下分配512bytes
    void *p4 = alloc::allocate(512);
    alloc::deallocate(p4,512);
#endif

// GUNC高版本如下
#ifdef __GUNC__ 
   // 以下两函数都是non-static,定要通过object调用，以下分配7个int
    int *p4 = allocator<int>().allocate(7);
    allocator<int>().deallocate((int*)p4,7)

   // 以下两函数都是non-static,定要通过object调用，以下分配9个int
    void *p5 = _gun_cxx::__pool_alloc<int>().allocate(9);
    __gun_cxx::__pool_alloc<int>().deallocate((int*)p5,9);
#endif
```
#### __new__ expression && __delete__ expression
*  __new__ expression
__new__ 的底层为malloc

```C++
complex *pc = new complex(1,2);

//编译器装换为
complex *pc;
try{
    void * mem = operator new(sizeof(complex));
    pc = static_cast<complex*>(mem);   // 类型转换
    pc->complex::complex(1,2);  //注意，只有编译器才可以像这样直接呼叫ctor(与编译器类型有关)
}
catch(std::bad_alloc){
    //若allocation失败就不执行constructor
}

// operator new
void *operator(size_t size,const std::nothrOw_t&THROW0()){
	void *p;
	while((p=malloc(size))==0){
	_TRY_BEGIN
		if(_callnewh(size)==0) break;
	_CATCH(std::bad_alloc) return(0);
	_CATCH_END
	}
	return (p);
}
```

* __delete__ expression

  __delete__的底层为free

```C++
delete pc;
	
//编译器转换为
pc->~complex(); //先析构
operator delete(pc); //再释放
	
// operator delete
void _cdecl operator delete(void*p) _THROW0(){
    free(p);
}
```

####  array new && array delete

用法
```C++
complex *pca = new complex[3];
delete []pca;
```

如果只写`delete pca`可能导致内存泄漏(只dtor了一个object)

使用array new时，由于没有初始化，所以类必须要有默认构造函数，如果没有默认构造函数，将报错。

* array size,in memory block

  <img src=".\img\image-20210712155816703.png" alt="image-20210712155816703" style="zoom:100%;" />

<img src=".\img\image-20210712162400208.png" alt="image-20210712162400208" style="zoom:67%;" />

####  placement new

placement new允许我们将object建构于allocator memory中。placement new不会再分配内存。

```C++
#include <new>
char *buf = new char[sizeof(complex)*3];
complex *pc = new(buf)complex(1,2);
···
delete []buf;

//编译器转换为：
complex *pc;
try{
    void * mem = operator new(sizeof(complex),buf);
    pc = static_cast<complex*>(mem);   // 类型转换
    pc->complex::complex(1,2);  //注意，只有编译器才可以像这样直接呼叫ctor(与编译器类型有关)
}
catch(std::bad_alloc){
    //若allocation失败就不执行constructor
}
```



####  重载

<img src=".\img\image-20210712163905743.png" alt="image-20210712163905743" style="zoom:45%;" />

```C++
#include <iostream>
using namespace std;

class Foo{
public:
    int _id;
    long _data;
    string _str;

public:
    Foo(): _id(0) {cout<<"default ctor.this="<<this<<" id="<<_id<<endl;}
    Foo(int i): _id(i) {cout<<"default ctor.this="<<this<<" id="<<_id<<endl;}

    ~Foo()  {cout<<"default dtor.this="<<this<<" id="<<_id<<endl;}
    
    static void* operator new(size_t size){
        Foo *p = (Foo*)malloc(size);
        cout<<"new()"<<endl;
        return p;
    }
    static void* operator new[](size_t size){
        Foo *p = (Foo*)malloc(size);
        cout<<"new()"<<endl;
        return p;
    }
    static void operator delete(void* pdead,size_t size){
        cout<<"del()"<<endl;
        free(pdead);
    }
    static void operator delete[](void* pdead,size_t size){
        cout<<"del[]"<<endl;
        free(pdead);
    }
};

int main(){
    Foo* pf = new Foo;
    delete pf;
    cout<<"-------------------------------------"<<endl;
    Foo* pf1 = new Foo[3];
    delete[]pf1;

    cout<<"-------------------------------------"<<endl;
    Foo* pf2=::new Foo;
    ::delete pf2;
    return 0;
}
```

>new()
>default ctor.this=0x7d13c0 id=0
>default dtor.this=0x7d13c0 id=0
>del()
>\-------------------------------------
>new()
>default ctor.this=0x7d2748 id=0
>default ctor.this=0x7d2770 id=0
>default ctor.this=0x7d2798 id=0
>default dtor.this=0x7d2798 id=0
>
>default dtor.this=0x7d2770 id=0
>default dtor.this=0x7d2748 id=0
>del[]
>\-------------------------------------
>default ctor.this=0x7d13c0 id=0
>default dtor.this=0x7d13c0 id=0

