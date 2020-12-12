#### 对象的本质

##### 问题1：一个NSObject对象所占用的内存空间是多少？

1. Objective-C代码，底层实现其实都是C\C++代码。Objective-C的[**面向对象**](https://mp.weixin.qq.com/s/MSISZP_LyiQVek0RU09vcg)是**基于C\C++的结构体**来实现的。

2. 将OC代码转换为iOS平台、arm64架构下的C++代码的**编译器命令**如下

   ```
   xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc main.m -o main.cpp
   ```

3. ![image-20201206111017053](/Users/grant/Library/Application Support/typora-user-images/image-20201206111017053.png)

4. **一个NSObject 对象占用的内存空间**，Cocoa Foundation 框架硬性规定为 **16个字节**。系统分配了16个字节给NSObject对象（通过malloc_size函数获得），但NSObject对象内部只使用了8个字节的空间（64bit环境下，可以通过class_getInstanceSize函数获得）

   ```objective-c
   size_t instanceSize(size_t extraBytes) const {
           if (fastpath(cache.hasFastInstanceSize(extraBytes))) {
               return cache.fastInstanceSize(extraBytes);
           }
   
           size_t size = alignedInstanceSize() + extraBytes;
           // CF requires all objects be at least 16 bytes.
           if (size < 16) size = 16;
           return size;
       }
   ```

5. 使用 `object_getClass` 可以获取类对象、元类对象。而`objc_getClass` 、`class` 方法仅仅是获得类对象