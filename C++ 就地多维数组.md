# C++ 就地多维数组

通常，一个多维的C++静态数组的声明如下：

~~~C++
int a[5][10]
~~~

当长度可变时，我们需要用 std::vector ，而且需要手动构建：

~~~C++
int n1 = 5;
int n2 = 6;
std::vector<std::vector<int>> v;
v.resize(n1);
for(auto& vv : v)
    vv.resize(n2);
~~~

这会浪费时间，因为会有多个内存分配，如果能分配一次就好了。但是这不起作用：

~~~C++
std::vector<int> v(1000);
int** n = (int**)v.data();
n[0][0]=5; // boom
~~~

它崩溃了，我们必须使用 for 循环进行初始化。



### 代码

C++ 11和可变参数模板来帮我：

~~~C++
template <typename T = char> size_t CreateArrayAtMemory(void*,size_t bs)
{
    return bs*sizeof(T);
}

template <typename T = char,typename ... Args> size_t
CreateArrayAtMemory(void* p, size_t bs, Args ... args)
{
    size_t R = 0;
    size_t PS = sizeof(void*);
    char* P = (char*)p;
    char* P0 = (char*)p;
    
    size_t BytesForAllPointers = bs*PS;
    R = BytesForAllPointers;
    
    char* pos = P0 + BytesForAllPointers;
    for(size_t i = 0;i < bs;i++){
        char** pp (char**)P;
        if(p)
            *pp = pos;
        size_t RLD = CreateArrayAtMemory<T>(p ? pos : nullptr, args ...);
        P += PS;
        R += RLD;
        pos += RLD;
    }
    return R;
}
~~~

这个递归函数接收许多参数（声明数组的维度）并修改传递给它的内存指针，因此将其转换为多维数组是安全的。如果 nullptr 传递，则返回分配所需的字节。

例如，一维数组：

~~~C++
int j = 0x21;
size_t n1 = CreateArrayAtMemory(nullptr,2);
vector<char> al(n1);
char* f1 = (char*)a1.data();
CreateArrayAtMemory(f1,2);
for(int i1 = 0;i1 < 2;i1++)
{
	f1[i1] = j++;
}
for(int i1 = 0;i1 < 2;i1++){
    std::cout << (int)f1[i1] << " ";
}
std::cout << std::endl << n1 << " bytes used" << std::endl;
~~~

预期输出：

~~~C++
33 34
2 bytes used
~~~

现在是 shorts 型的二维数组：

~~~C++
int j = 0x21;
size_t n2 = CreateArrayAtMemory<short>(nullptr,2,3);
vector<char> a2(n2);
short** f2 = (short**)a2.data();
CreateArrayAtMemory(f2,2,3);
for(int i1 = 0;i1 < 2;i1++)
{
    for(int i2 = 0;i2 < 3;i2++)
    {
        f2[i1][i2] = j++;
    }
}
// Dump
for(int i1 = 0;i1 < 2;i1++)
{
	for(int i2 = 0;i2 < 3;i2++)
    {
        std::cout << (int)f2[i1][i2] << " ";
    }    
}
std::cout << std::endl << n2 << " bytes used" << std::endl;
std::cout << std::endl;
~~~

预期输出：

~~~C++
33 34 35 36 37 38
20 bytes used
~~~