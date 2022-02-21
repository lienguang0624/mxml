# mxml 安装
## 1.解压
```c
 tar -xf mxml-2.10.tar 
 ```
## 2.安装 
```c
./configure
```
&emsp;&emsp;如果需要交叉编译自行添加后缀--host=arm-linux CC=arm-linux-gnueabihf-gcc CXX=arm-linux-gnueabihf-g++
```c
make
sudo make install
```

&emsp;&emsp;与一般安装一样。先./configure。

&emsp;&emsp;再执行make，最后执行sudo make install，安装成功。

&emsp;&emsp;默认就是安装在/usr/local的，如果安装在其它位置，要在/etc/ld.so.conf.d/libc.conf文件配置上路径。

&emsp;&emsp;如果共享库文件安装到了/lib或/usr/lib目录下, 那么需执行一下ldconfig命令

&emsp;&emsp;ldconfig命令的用途, 主要是在默认搜寻目录(/lib和/usr/lib)以及动态库配置文件/etc/ld.so.conf内所列的目录下,搜索出可共享的动态链接库(格式如lib*.so*), 进而创建出动态装入程序(ld.so)所需的连接和缓存文件. 缓存文件默认为/etc/ld.so.cache, 此文件保存已排好序的动态链接库名字列表. 


## 3.使用
&emsp;&emsp;使用的话，加上#include <mxml.h>头文件就行。

## 4.编译
```c
$ gcc create_xml.c -o create
```
&emsp;&emsp;这样不行，会报错，需要引用mxml库
```c
gcc create_xml.c -lmxml -o create
```
&emsp;&emsp;如果需要交叉编译
```c
arm-linux-gnueabihf-gcc xml_test.c -I/usr/local/include -L/usr/local/lib -lmxml -o xml_test
```

# mxml常用函数解析
```c
tree = mxmlLoadFile(NULL, fp, MXML_OPAQUE_CALLBACK);
```

```c
pIndex = mxmlIndexNew(tree,"ExtenPhoneDialPlan",NULL);
```

```c
pExtenPhoneDialPlan = mxmlIndexEnum(pIndex)
```

```c
mxmlFindElement(tree,tree,"sysExtenPhoneDialPlan",NULL,NULL,MXML_DESCEND)
```

```c
pstrRuleAccount = mxmlElementGetAttr(pExtenPhoneDialPlan,"RuleAccount")
```

# 读取常值及变量值
```c
#include<string.h>
#include<stdio.h>
#include<stdlib.h>
#include <fcntl.h>
#include "D:\Tornado2.2\XML\xmlTest\src\vxw5\config.h"
#include"D:\Tornado2.2\XML\xmlTest\src\mxml.h"
 
#define VXWORKS 
 
int vmain()
{
    FILE *fp;
    mxml_node_t *tree,*node;
    mxml_node_t *id,*password;
 
    fp = fopen("host:d:\\Tornado2.2\\XML\\debug_settings.xml", "r");
 
    tree = mxmlLoadFile(NULL, fp,MXML_TEXT_CALLBACK);
    fclose(fp);
 
    node = mxmlFindElement(tree, tree, NULL, NULL, NULL,MXML_DESCEND);
 
    printf(" year:%s \n",mxmlElementGetAttr(node,"year"));
    printf(" date:%s \n",mxmlElementGetAttr(node,"date"));
    printf(" month:%s \n",mxmlElementGetAttr(node,"month"));
 
    id = mxmlFindElement(node, tree, "id",NULL, NULL,MXML_DESCEND);
    printf("[%s}\n",id->child->value.text.string);
 
    password = mxmlFindElement(node, tree, "password",NULL, NULL,MXML_DESCEND);
 
    printf("[%s]\n",password->child->value.text.string);
 
    mxmlDelete(tree);
 
    return 0 ;
}
```
XML文件如下所示：
```c
<?xml version="1.0" encoding="gb2312" ?> 
 <note year="55" date="33" month="22">
  <id>5000</id> 
  <password>FE-D0-18-00</password> 
</note>
```



# 按序检索

```c
#include<stdio.h>
#include"mxml.h"

int main(void)
{
    FILE* fp = fopen("prac.xml","r");
    //从prac.xml文件中加载xml
    mxml_node_t *xml = mxmlLoadFile(NULL,fp,MXML_NO_CALLBACK);
    //定义两个空节点
    mxml_node_t *book = NULL;
    mxml_node_t *title = NULL;
    //从xml开始向下查找 name=book attrr=category
    book = mxmlFindElement(xml,xml,"book","category",NULL,MXML_DESCEND);

    while(book)
    {
        //获取title子元素的文本 book元素的属性
        title = mxmlFindElement(book,xml,"title",NULL,NULL,MXML_DESCEND);
        if(title == NULL)
        {
            printf("title not found\n");
            continue;
        }
        else
        {
            printf("book'titele is %s\n",mxmlGetText(title,NULL));
            printf("book'category:%s\n",mxmlElementGetAttr(book,"category"));
            book = mxmlFindElement(title,xml,"book","category",NULL,MXML_DESCEND);
        }
    }
    mxmlDelete(xml);
    fclose(fp);
    return 0;
}
```
XML文件如下所示：
```c
<?xml version="1.0" encoding="utf-8"?>
<bookstore>
    <book category="CHILDREN">
        <title>Harry.Potter</title>
        <author>JK.Rowling</author>
        <year>2005</year>
        <price>29.99</price>
    </book>
    <book category="WEB">
        <title>LearningXML</title>
        <author>ErikT.Ray</author>
        <year>2003</year>
        <price>39.95</price>
    </book>
</bookstore>
```