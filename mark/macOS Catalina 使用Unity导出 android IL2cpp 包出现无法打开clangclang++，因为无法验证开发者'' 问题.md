## macOS Catalina 使用Unity导出 android IL2cpp 包出现无法打开clang/clang++，因为无法验证开发者'' 问题

这个问题是由于ndk里的clang和clang++导致的

解决办法：

- 前往ndk里的clang 文件所在目录

![image-20201028101618857](/Users/admin/Library/Application Support/typora-user-images/image-20201028101618857.png)

- 右键选择使用终端打开

  ![image-20201028102143467](/Users/admin/Library/Application Support/typora-user-images/image-20201028102143467.png)

- 两个文件使用这种方式打开后，重新打包就行

