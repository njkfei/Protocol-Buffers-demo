# Protocol buffers demo
 protocol是非常优秀序列化的方案，在BAIDU内部大量使用，58同城和58到家的业务量上也大量使用了pb。
 pb协议特点：
* 信息有效率率非常高
* 采用二进制协议，所以效率高嘛
* 节省带宽（和前面是一样的）
* 跨语言支持，支持JAVA,C++,PYTHON,GO,PHP等主流开发语言

   本DEMO是一个小例子，基于maven进行构建，直接拿官方DEMO改的。

##  pb安装
### windows
 下载(https://github.com/google/protobuf/releases/download/v2.6.1/protoc-2.6.1-win32.zip)[https://github.com/google/protobuf/releases/download/v2.6.1/protoc-2.6.1-win32.zip]
 解压即可。
 
### linux安装
  下载(https://github.com/google/protobuf/releases/download/v2.6.1/protobuf-2.6.1.tar.gz)[https://github.com/google/protobuf/releases/download/v2.6.1/protobuf-2.6.1.tar.gz]
  解压即可。
   国内网速比较慢，推荐找一台AWS的服务器下载，再中转到本地。
  
  ## 协议编写
    pb协议语法格式比较简单，如下所示：
  ```
  package tutorial;

option java_package = "com.example.tutorial";
option java_outer_classname = "AddressBookProtos";

message Person {
  required string name = 1;
  required int32 id = 2;
  optional string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    required string number = 1;
    optional PhoneType type = 2 [default = HOME];
  }

  repeated PhoneNumber phone = 4;
}

message AddressBook {
  repeated Person person = 1;
}
```
假设上述文件名为AddressBook.proto

## 编译协议
在windows下面，进入pb目录，直接运行
```
protoc.exe --java_out=. AddressBook.proto
```
如果想省事，可以将pb目录加入到环境变量。

## maven依赖

```
        <dependency>
            <groupId>com.google.protobuf</groupId>
            <artifactId>protobuf-java</artifactId>
            <version>2.5.0</version>
        </dependency>
```

## pb序列化
```
import com .example.tutorial.AddressBookProtos.AddressBook;
import com .example.tutorial.AddressBookProtos.Person;
import com .example.tutorial.AddressBookProtos.*;

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.FileOutputStream;

/**
 * Created by njp on 2016/5/11.
 */
public class AppWrite {
    public static void main(String[] args ) throws Exception{
        Person.PhoneNumber.Builder phone  = Person.PhoneNumber.newBuilder().setNumber("15910534204");

        Person.Builder person = Person.newBuilder().addPhone(phone).setId(1).setEmail("njp@sanhao.com")
                .setName("njp");
        AddressBook.Builder ab = AddressBook.newBuilder().addPerson(person);

        FileOutputStream fo = new FileOutputStream("hello");
        ab.build().writeTo(fo);
        fo.close();

        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        ab.build().writeTo(byteArrayOutputStream);

        System.out.println(byteArrayOutputStream.toByteArray().length);

        AddressBook nab = AddressBook.parseFrom(byteArrayOutputStream.toByteArray());

        Person ps = nab.getPerson(0);

        System.out.println(ps.getId());
        System.out.println(ps.getName());
        System.out.println(ps.getEmail());

        Person.PhoneNumber phoneNumber = ps.getPhone(0);

        System.out.println(phoneNumber.getNumber());

        byteArrayOutputStream.close();
    }
}
```

## pb反序列化

```
import com.example.tutorial.AddressBookProtos.AddressBook;
import com.example.tutorial.AddressBookProtos.Person;

import java.io.FileInputStream;
import java.io.FileOutputStream;

public class AppRead {
    public static void main(String[] args ) throws Exception{
        FileInputStream fi = new FileInputStream("hello");
        AddressBook ab = AddressBook.parseFrom(fi);

        Person person = ab.getPerson(0);

        System.out.println(person.getId());
        System.out.println(person.getName());
        System.out.println(person.getEmail());

        Person.PhoneNumber phoneNumber = person.getPhone(0);

        System.out.println(phoneNumber.getNumber());


        fi.close();
    }
}
```
