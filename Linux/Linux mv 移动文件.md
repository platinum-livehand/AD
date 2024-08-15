## 文件移动

```shell
mv source_file destination_file
```

​	`source_file`**可以是目录也可以是文件**，`destination_file` **必须是目录且必须存在**。

## 文件改名

```shell
mv original_name new_name
```

​	`original_name`**可以是文件也可以是目录**，并且是**存在的**，`new_name`**不存在**。

![](..\picture\Linux-1.png)

## 文件覆盖

```shell
mv source_file destination_file
```

​	`source_file` 是文件并且存在，`destination_file` 是文件也存在。`source_file`文件中的内容覆盖`destination_file`文件中的内容，`source_file`文件被删除，只剩下`destination_file`文件。