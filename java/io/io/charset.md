# 编码
### 有关编码的基础知识
* 位 bit 最小的单元: 字节 byte 机器语言的单位 1byte=8bits
    > 1KB=1024byte  
    > 1MB=1024KB  
    > 1GB=1024MB  
* 进制
    > 二进制 binary  
    > 八进制 octal  
    > 十进制 decimal  
    > 十六进制 hex  
* 字符：是各种文字和符号的总称,包括各个国家的文字,标点符号,图形符号,数字等
* 字符集：字符集是多个符号的集合, 每个字符集包含的字符个数不同
* 字符编码：字符集只是规定了有哪些字符,而最终决定采用哪些字符,每一个字符用多少字节表示等问题,则是由编码来决定的.计算机要准确的处理各种字符集文字,需要进行字符编码,以便计算机能够识别和存储各种文字

### 常见的编码表
* ASCII：美国标准信息交换码。用一个字节的7位可以表示。
* ISO8859-1：拉丁码表。欧洲码表,用一个字节的8位表示。
* GB2312：中国的中文编码表。最多两个字节编码所有字符
* GBK：中国的中文编码表升级，融合了更多的中文文字符号。最多两个字节编码
* Unicode：国际标准码，融合了目前人类使用的所有字符。为每个字符分配唯一的字符码。所有的文字都用两个字节来表示。
* UTF-8：变长的编码方式，可用1-4个字节来表示一个字符。

**Unicode字符集只是定义了字符的集合和唯一编号，Unicode编码，则是对UTF-8、UCS-2/UTF-16等具体编码方案的统称而已，并不是具体的编码方案。**