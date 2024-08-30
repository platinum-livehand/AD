## grep（Global Regular Expression Print）

​	和 `find` 不同 `grep` 命令用于查找文件里符合条件的字符串，`grep` 命令中有几个常用的参数。

- `-r`：如果需要搜索目录中的文件内容，需要进行递归操作，必须指定该参数。
- `-i`：对应要搜索的关键字，忽略字符大小写的差别。
- `-n`：在显示符合样式的那一行之前，标示出该行的列数编号。

​	对应要搜索的文件内容，建议放在引号中，因为关键字中可能有特殊字符，或者有空格，从而导致解析错误。

```shell
# 语法格式: 
$ grep "搜索的内容" 搜索的路径/文件 参数
```

```shell
# 搜索指定文件中是否有字符串 include
$ grep "include" testgcc.c
#include <stdio.h>
#include <stdlib.h>

# 不区分大小写进行搜索
$ grep "INCLUDE" testgcc.c
$ grep "INCLUDE" testgcc.c -i
#include <stdio.h>
#include <stdlib.h>

# 搜索指定目录中哪些文件中包含字符串 include 并且显示关键字所在的行号
$ grep "include" ./ -rn
./myprogram.c:1:#include <iostream>
./testgcc.c:1:#include <stdio.h>
./testgcc.c:2:#include <stdlib.h>
```

