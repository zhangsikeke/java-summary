# java通过JNI调用c++接口

#### 定义java native方法

```java
package com.zsk.jni;

public class OpenItf {
    public static native int add(int a, int b);

    public static native void sayHello();

    public static native String getRandomString();

}
```

#### 生成头文件并实现c++功能

```shell
javac OpenItf.java
#在返回com所在的包执行javah生成头文件，否则会报错
javah com.zsk.jni.OpenItf 
#生成的头文件需要两个文件jni.h和jni_md.h
#jni.h在jdk1.8.0_171\include包下
#jni_md.h在jdk1.8.0_171\include\win32包下
```

```c
//com_zsk_jni_OpenItf.h

#include "jni.h"
#ifndef _Included_com_zsk_jni_OpenItf
#define _Included_com_zsk_jni_OpenItf
#ifdef __cplusplus
extern "C" {
#endif

JNIEXPORT jint JNICALL Java_com_zsk_jni_OpenItf_add
  (JNIEnv *, jclass, jint, jint);
    
JNIEXPORT void JNICALL Java_com_zsk_jni_OpenItf_sayHello
  (JNIEnv *, jclass);
    
JNIEXPORT jstring JNICALL Java_com_zsk_jni_OpenItf_getRandomString
  (JNIEnv *, jclass);

#ifdef __cplusplus
}

#endif
#endif

//com_zsk_jni_OpenItf.cpp实现
#include <iostream>
#include <stdlib.h>
#include <time.h>
#include <string>
#include "com_zsk_jni_OpenItf.h"

#define STR_LEN 32
#define CHAR_MIN 'a' //定义随机输出的字符串长度。
#define CHAR_MAX 'z' //定义输出随机字符串每个字符的最大最小值。
JNIEXPORT jint JNICALL Java_com_zsk_jni_OpenItf_add(JNIEnv * env, jclass clazz,
		jint a, jint b) {
	return a + b;
}

JNIEXPORT void JNICALL Java_com_zsk_jni_OpenItf_sayHello(JNIEnv * env,
		jclass clazz) {
	std::cout << "hello,I am from c++ dll lib" << std::endl;
}

JNIEXPORT jstring JNICALL Java_com_zsk_jni_OpenItf_getRandomString(JNIEnv *env,
		jclass clazz) {
	jstring jstr;
	char str[STR_LEN + 1] = { 0 };
	int i;
	//通过时间函数设置随机数种子，使得每次运行结果随机。
	srand(time(NULL));
	for (i = 0; i < STR_LEN; i++) {
		str[i] = rand() % (CHAR_MAX - CHAR_MIN + 1) + CHAR_MIN; //生成要求范围内的随机数。
	}
	jstr = env->NewStringUTF(str);
	return jstr;
}
```

#### 编译（使用了MinGW编译，请安装MInGW，注意64位的jre无法加载32位的dll，编译时要保持一致）

```shell
g++ -O3 -Wall -c -fmessage-length=0 -o src\com_zsk_jni_OpenItf.o ..\src\com_zsk_jni_OpenItf.cpp
#注意一定要加参数-Wl,--add-stdcall-alias，否则调用会报JNI Exception in thread "main" java.lang.UnsatisfiedLinkError错误
g++ -Wl,--add-stdcall-alias -shared -o libCTestJava.dll src\com_zsk_jni_OpenItf.o
```

#### 源码

https://gitee.com/zhangsike/java-leaning/tree/master/java-jni-call-cpp

