# X-1、Linux进程长参数编程

## (一) 短选项扩展编程

### 1. 再论进程参数 (短选项)

>```tex
>🤪注释:进程参数 多数情况下也会称作 启动参数 和 命令行参数
>```
>
>- 由 选项 , 选项值 , 操作数 组成
>- 选项由短横线 ( `-` ) 开始 , 选项名必须是单个字母或单个数字字符
>- 选项可以有选项值 , 选项与选项值之间可用空格分隔 ( `-o test` <==> `-otest` )
>- 如果多个选项均无选项值 , 可合而为一 ( `-a -b -c` <==> `-abc` )
>- 既不是选项 , 也不能作为选项值的参数是操作数
>- 第一次出现的双横线 ( `--` ) 用于结束所有选项 , 后续参数为操作数

### 2. 进程短选项示例

>- 规则 : `if:s::`
>  -  `s::` 表示 `-s` 不一定需要选项值 , 若有选项值只能以 `-svalue` 的方式指定 **(`-svalue`必须连在一起写 , 不能有空格)**
>- 实例 : 
>
><img src="X-1、Linux进程长参数编程.assets/image-20230724114724403.png" alt="image-20230724114724403" />
>
>[短参数short.cpp参考代码](https://github.com/WONGZEONJYU/Linux_System_Program/blob/main/x-1_Process_Parameter/short.cpp)
>
>![image-20230724114829775](X-1、Linux进程长参数编程.assets/image-20230724114829775.png)

## (二) 进程长参数编程

### 1. 进程长参数示例

><img src="X-1、Linux进程长参数编程.assets/image-20230724114919596.png" alt="image-20230724114919596" />
>
>```tex
>‼️ ‼️ ‼️ 选项名和选项值之间可以用 ("=") 或者空格 (" ")
>```

### 2. 进程长参数规范

>- 由选项 , 选项值 , 操作数组成
>- 选项由双横线 (`--`) 开始 , **$\color{SkyBlue}{选项名可以是有意义的字符序列}$**
>- 选项可以有选项值 , 选项与选项值可用 **$\color{red}{空格 / 等号}$** 分隔 ( `--demo test` <==> `--demo=test` )
>- 既不是选项 , 也不能作为选项值的参数是操作数
>- 第一次出现的双横线( `--` ) 用于结束所有选项 , 后续参数为操作数

### 3. Linux启动长参数 (命令行长参数) 编程

><img src="X-1、Linux进程长参数编程.assets/image-20230724115351202.png" alt="image-20230724115351202" />
>
><img src="X-1、Linux进程长参数编程.assets/image-20230724115356292.png" alt="image-20230724115356292" />
>
>- `getopt_long(…)` 从argc和argv中获取下一个选项
>- `getopt_long(…)` $\color{red}{同时支持}$ **$\color{red}{短选项}$** $\color{red}{和}$ **$\color{red}{长选项}$**
>   - 短选项 : 通过 $\color{red}{规则字符串}$ 指定支持的选项
>   - 长选项 : 通过 `struct option` $\color{red}{结构体}$ 指定支持的选项
>- 默认情况下 : `getopt_long(…)` 对 argc 进行重排 , 所有操作数位于最后位置
>

#### (1) 长参数编程示例

><img src="X-1、Linux进程长参数编程.assets/image-20230724115732284.png" alt="image-20230724115732284" />
>
>[长参数long.cpp](https://github.com/WONGZEONJYU/Linux_System_Program/blob/main/x-1_Process_Parameter/long.cpp)
>
><img src="X-1、Linux进程长参数编程.assets/image-20230724115804176.png" alt="image-20230724115804176" />
>
><img src="X-1、Linux进程长参数编程.assets/image-20230724115811022.png" alt="image-20230724115811022" />
>
><img src="X-1、Linux进程长参数编程.assets/image-20230724115817209.png" alt="image-20230724115817209" />

#### (2) 另一种长参数编程模式

><img src="X-1、Linux进程长参数编程.assets/image-20230724115836060.png" alt="image-20230724115836060" />
>
>[另外一种长参数编程](https://github.com/WONGZEONJYU/Linux_System_Program/blob/main/x-1_Process_Parameter/long-new.cpp)
>
><img src="X-1、Linux进程长参数编程.assets/image-20230724115915453.png" alt="image-20230724115915453" />
>
><img src="X-1、Linux进程长参数编程.assets/image-20230724115922016.png" alt="image-20230724115922016" />

## (三) 进程参数变种玩法 ==> 统一长短参数

>`int getopt_long_only(argc, argv[], optstring, longopts, longindex);`
>
>- 对于以单横线 ( `-` ) 起始的参数
>   - 直接查找长参数选项 , 若查找失败 , 则查找短参数选项
>- 对于以双横线 ( `--` ) 起始的参数
>   - 直接查找长参数选项 , 若查找失败 , 则查找失败
>

#### 1. 统一长短参数示例

>![image-20230724120156029](X-1、Linux进程长参数编程.assets/image-20230724120156029.png)
>
>[统一长短参数long-only.cpp](https://github.com/WONGZEONJYU/Linux_System_Program/blob/main/x-1_Process_Parameter/long-only.cpp)
>
><img src="X-1、Linux进程长参数编程.assets/image-20230724120359357.png" alt="image-20230724120359357" />

#### 2. 进程参数编程总结

><img src="X-1、Linux进程长参数编程.assets/image-20230724120418262.png" alt="image-20230724120418262" />

