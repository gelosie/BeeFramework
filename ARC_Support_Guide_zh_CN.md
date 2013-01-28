#ARC 项目中使用 BeeFramwrok 说明

BeeFramework 项目的原始代码是基于 non-arc，应用在 ARC 项目中，两个办法：

- 编译静态库
- 使用 -fno-objc-arc 编译标记

#ARC 支持分支


## 取得 ARC 支持代码

- mkdir _WORK_DIR_
- cd _WORK_DIR_
- git clone https://github.com/gavinkwoe/BeeFramework.git
- cd BeeFramework
- git checkout arc

## 项目结构

- 打开工作空间 BeeFramework.xcworkspace，三个项目： 
	 - WhatsBug
	 - StaticLibrary
	 - WhatsBugARC
- arc 分支增加了 WhatsBugARC 项目，是基于  WhatsBug 项目的 ARC 代码
- 当下 WhatsBugARC 项目是使用  -fno-objc-arc 来引用  BeeFramework 的代码的

## 工作
- 兼容 ARC 和 non-ARC 代码，主要是使用 Bee_Precompile.h 中添加的宏：

<pre><code>
#if __has_feature(objc_arc)
    #define BEE_PROP_RETAIN strong
    #define BEE_RETAIN(x) (x)
    #define BEE_RELEASE(x)
    #define BEE_AUTORELEASE(x) (x)
    #define BEE_BLOCK_COPY(x) (x)
    #define BEE_BLOCK_RELEASE(x)
    #define BEE_SUPER_DEALLOC()
    #define BEE_AUTORELEASE_POOL_START() @autoreleasepool {
    #define BEE_AUTORELEASE_POOL_END() }
#else
    #define BEE_PROP_RETAIN retain
    #define BEE_RETAIN(x) ([(x) retain])
    #define BEE_RELEASE(x) ([(x) release])
    #define BEE_AUTORELEASE(x) ([(x) autorelease])
    #define BEE_BLOCK_COPY(x) (Block_copy(x))
    #define BEE_BLOCK_RELEASE(x) (Block_release(x))
    #define BEE_SUPER_DEALLOC() ([super dealloc])
    #define BEE_AUTORELEASE_POOL_START() NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    #define BEE_AUTORELEASE_POOL_END() [pool release];
#endif
</code></pre>
- 主要工作就是使用上面的宏替换 BeeFramework 中的内存管理代码，去了 以支持WhatsBugARC中的 -fno-objc-arc 编译标签，以支持 WhatsBug 和 WhatsBugARC 项目。

## 最后
- 欢迎大家贡献代码，向 arc 分支 pull request 代码
