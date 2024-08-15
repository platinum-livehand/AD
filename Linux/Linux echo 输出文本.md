## 输出文本

​	`echo` 命令的作用是向用户显示一行文本。它可以用于各种情况，比如在脚本中输出提示信息、打印变量值、生成日志文件等等。

## 输出字符串

```shell
qy@ubuntu:~/linux_command_train$ echo "Hello World"
Hello World
```

## 输出特殊字符

​	有些字符在 `shell` 中具有特殊含义，比如换行符 `\n` ，制表符 `\t` 等。如果需要输出这些特殊字符本身而不是其含义，可以使用转移字符 `\`。例如，输出包含换行符的字符串。

```shell
qy@ubuntu:~/linux_command_train$ echo "Hello\nWorld"
Hello\nWorld
qy@ubuntu:~/linux_command_train$ echo -e "Hello\nWorld"
Hello
World
```

## 输出变量

​	除了输出固定的字符串，echo 命令还可以输出变量的值。只需要将变量名放在引号内即可。

```shell
qy@ubuntu:~/linux_command_train$ name="Tom"
qy@ubuntu:~/linux_command_train$ echo "Hello $name"
Hello Tom
```

