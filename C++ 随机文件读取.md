# C++ 随机文件 I/O

文件指针：

每个文件流泪都包含一个文件指针，用来跟踪文件内当前读写位置。

默认情况下，打开文件进行读取或者写入时，文件指针设置在文件的开头。

追加模式下，文件指针将移动到文件末尾。

使用 seekg() 和 seekp() 访问随机文件：

目前为止，我们所做的文件访问操作都是顺序的，但是，也可以进行随机文件访问——即，跳转到文件中的各个点以读取内容。

随机文件访问是通过 seekg() 函数（用于输入）和 seekp() 函数（用于输出）操作文件指针来完成的。

seekg() 和 seekp() 函数采用两个参数，第一个是一个偏移量，决定移动文件指针的字节数，第二个是一个 IOS 标志，指定偏移参数应该从什么地方开始偏移。

| IOS seek 标志 | 意义                           |
| ------------- | ------------------------------ |
| beg           | 偏移量相对于文件开头（默认）   |
| cur           | 偏移量相对于文件指针的当前位置 |
| end           | 偏移量相对于文件末尾           |

正偏移量意味着将文件指针移向文件末尾，负偏移量意味着将文件指针移向文件开头。

~~~C++
inf.seekg(14, ios::cur); // 从文件指针往文件末尾方向移动 14 bytes
inf.seekg(-18, ios::cur); // 从文件指针往文件开头方向移动 18 bytes
inf.seekg(22, ios::beg); // 从文件开头往文件末尾方向移动 22 bytes
inf.seekg(24); // 从文件开头（默认）往文件末尾移动 24 bytes
inf.seekg(-28, ios::end); // 从文件末尾往文件开头方向移动 28 bytes
~~~

移动到文件的末尾或开头：

~~~C++
inf.seekg(0, ios::beg); // 移动到文件开头
inf.seekg(0, ios::end); // 移动到文件末尾
~~~



~~~c++
int main(){
	std::ifstream inf("sample.dat");
    
    if(!inf)
    {
        std::cerr << "sample.data could not be opened" << std::endl;
        return -1;
    }
    
    std::string strData;
    
    inf.seek(5);
    
    getline(inf, strData);
    std::cout << strData << '\n';
    
    inf.seekg(8, ios::cur);
    std::getline(inf, strData);
    std:: cout << strData << '\n';
    
    inf.seekg(-15, ios::end);
    std::getline(inf, strData);
    std::cout << strData << '\n';
    
    return 0;
}
~~~

另外有两个有用的函数 tellg() 和 tellp() ，他们返回文件指针的绝对位置，这可用于确认文件的大小：

~~~C++
std::ifstream inf("sample.dat");
inf.seekg(0, std::ios::end); // 移动至文件末尾
std::cout << inf.tellg(); //文件指针的绝对位置，以 bytes 为单位
~~~



#### 使用 fstream 同时读取和写入文件

fstream 类能够同时读取和写入文件，但是不能在读取和写入之间任意切换，在两者之间切换的唯一方式是执行修改文件位置的操作（例如查找）。如果实际上不想移动文件指针，可以随时寻找当前位置：

~~~c++
iofile.seekg(iofile.tellg(), ios::beg); // 查找当前位置
~~~

与 ifstream 不同，在 ifstream 中我们可以通过 while(inf) 判断是否有更多内容要读取，而 fstream 则不能。



以下程序会将文件中的元音字符替换为 '#' ：

~~~c++
int main(){
    std::fstream iofile("sample.dat");
    
    if(!iofile)
    {
    	std::cerr << "File sample.dat cound not be opened" << std::endl;
        return -1;
    }
    
    char chChar{};
    
    while(iofile.get(chChar))
    {
    	switch(chChar)
        {
            case 'a':
            case 'e':
            case 'i':
            case 'o':
            case 'u':
            case 'A':
            case 'E':
            case 'I':
            case 'O':
            case 'U':
                // 倒退一个字符
                iofile.seekg(-1, std::ios::cur);
                
                // 因为做了一次 seekg，我们现在可以安全地执行写入
                iofile << '#';
                
                // 现在我们返回读取模式，从而让下一个 get 函数操作顺利
                iofile.seekg(iofile.tellg(), std::ios::beg);
                
                break;
        }    
    }
}
~~~



要删除文件，可以使用 remove() 函数。此外如果流当前处于打开状态，is_open()函数将返回 true ，否则返回 false。

