# xv6-add-syscall
777を返すだけのシステムコール return_777を追加する。


## 結果
```
$ myprog
return 777
$
```


## Makefile
システムコール確認用プログラム myprog.c の追加

```
UPROGS=\
        $U/_cat\
        $U/_echo\
        (省略)
        $U/_myprog\
        (省略)
        $U/_zombie\        
```

## kernel
### sysproc.c
最終行に以下を追加。
```
uint64
sys_return_777(void)
{
  return 777;
}
```


### syscall.c
```
(省略)
extern uint64 sys_write(void);
extern uint64 sys_uptime(void);
extern uint64 return_777(void);
(省略)
```

```
(省略)
[SYS_mkdir]   sys_mkdir,
[SYS_close]   sys_close,
[SYS_return_777]   sys_return_777,
(省略)
```


### syscall.h
```
#define SYS_link   19
#define SYS_mkdir  20
#define SYS_close  21
#define SYS_return_777 22
```

## user
### usys.S
make, make qemu後に生成されるファイル。
usys.Sを生成するファイルが分かれば、そっちを変更するほうがよさそう。


最終行に以下を追加
```

.global return_777
return_777:
 li a7, SYS_return_777
 ecall
 ret
 ```
 
 ### user.h
```
// system calls
int fork(void);
(省略)
int uptime(void);
int return_777(void);
```

### myprog.c
```
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"
int main(void){
        printf("return %d\n", return_777());
        exit(1);
}
```

