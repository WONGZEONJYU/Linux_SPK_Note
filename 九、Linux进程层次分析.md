# 九、Linux进程层次分析

## (一) 详解Linux进程组

### 1. Linux进程组

>- $\color{red}{每个进程都有一个 进程组号 ( PGID )}$
>   - 进程组 : $\color{red}{一个或多个进程的集合}$ **( 集合中的进程并不孤立 )**
>   - 进程组中的进程通常存在 $\color{red}{父子关系 , 兄弟关系 , 或功能相近}$
>
>- 进程组可方便进程管理 ( 如 : 同时杀死多个进程，发送一个信号给多个进程)
>   - 每个进程必定属于一个进程组，也只能属于一个进程组
>   - 进程除了有 PID 外 , 还有 PGID ( 唯一 , 但可变 )
>   - 每个进程组有一个 $\color{red}{进程组长}$，$\color{SkyBlue}{进程组长的PID和PGID相同(PID == PGID)}$
>
>- `pid_t getpgrp(void);`  获取当前进程的组标识
>- `pid_t getpgid(pid_t pid);`  获取指定进程的组标识
>- 对于 `int setpgid(pid_t pid, pid_t pgid);` 设置pid进程的进程组标识为pgid : 
>   - 如果 pid == pgid , 将pid指定的进程设为组长
>   - 如果 pid == 0 , 设置当前进程的组标识为pgid
>   - 如果 pgid == 0 , 则将pid作为组标识
>
>
>```tex
>注释：
>   函数作用：setpgid(pid, pgid) 将pid进程的进程组ID设置成pgid，创建一个新进程组或加入一个已存在的进程组
>   函数性质:
>       性质1:一个进程只能为 自己或子进程 设置进程组ID，不能设置其父进程的进程组ID。
>       性质2:if(pid == pgid)，由pid指定的进程变成进程组长；即进程pid的进程组标识pgid为pid。
>       性质3:if(pid==0)，将当前进程的pid作为进程组ID。
>       性质4:if(pgid==0)，将pid作为进程组ID。
>```
>
>- **$\color{red}{默认情况下 , 子进程与父进程 属于 同一个进程组}$**

### 2. 进程组示例程序

><img src="九、Linux进程层次分析.assets/image-20230721174913135.png" alt="image-20230721174913135" />

### 3. 编程实验 : Linux进程组

>[pgid_j.cpp参考代码](https://github.com/WONGZEONJYU/Linux_System_Program/blob/main/8.Process_Hierarchy/pgid_j.cpp)
>
><img src="九、Linux进程层次分析.assets/image-20230721175036218.png" alt="image-20230721175036218" />
>
><img src="九、Linux进程层次分析.assets/image-20230721175049067.png" alt="image-20230721175049067" />

### 4. 深入理解进程组

>- 进程组长终止 , 进程组依然存在 $\color{SkyBlue}{(进程组长仅用于创建新进程组)}$ , $\color{red}{进程组中的 所有进程 结束之后 , 进程组消亡}$
>- 父进程创建子进程后立即通过 `setpgid()` 改变子进程的组标识 ( PGID ) , 子进程从父进程的进程组移动到组标识的进程组；同时 , 子进程也需要通过 setpgid() 改变自身组标识 (PGID)
>- 子进程 $\color{red}{如果调用}$ `exec()` , 那么 : 
>   - **父进程无法通过 `setpgid()` 改变子进程的组标识 (PGID)**
>   - **子进程只能自身通过 `setpgid()` 改变组标识 (PGID)**
>

#### (1) 进程组标识设置技巧

><img src="九、Linux进程层次分析.assets/image-20230721175606668.png" alt="image-20230721175606668" />
>
>```C+
>setpgrp() <===> setpgid(0,0)
>```

#### (2) 编程实验一

>[pgid_a.cpp代码参考](https://github.com/WONGZEONJYU/Linux_System_Program/blob/main/8.Process_Hierarchy/pgid_a.cpp)
>
><img src="九、Linux进程层次分析.assets/image-20230721175740636.png" alt="image-20230721175740636" />
>
><img src="九、Linux进程层次分析.assets/image-20230721175746990.png" alt="image-20230721175746990" />

#### (3) 编程实验二

>[与实验一同一个文件,注意屏蔽的代码,我是参考链接,我可以点开](https://github.com/WONGZEONJYU/Linux_System_Program/blob/main/8.Process_Hierarchy/pgid_a.cpp)
>
>1. 未加休眠
>
><img src="九、Linux进程层次分析.assets/image-20230721180140907.png" alt="image-20230721180140907" />
>
><img src="九、Linux进程层次分析.assets/image-20230721180148256.png" alt="image-20230721180148256" />
>
>2. 加休眠
>
><img src="九、Linux进程层次分析.assets/image-20230721180217310.png" alt="image-20230721180217310" />
>
>```tex
>注释:代码中的sleep(1),使得子进程先调用了evec(),父进程无法再通过setpgid()改变子进程的组标识(PGID)
>```
>
><img src="九、Linux进程层次分析.assets/image-20230721180253362.png" alt="image-20230721180253362" />

