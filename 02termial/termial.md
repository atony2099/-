## 目录操作

| 命令名    | 功能描述       | 使用举例             |
| ------ | ---------- | ---------------- |
| mkdir  | 创建一个目录     | mkdir dirname    |
| rmdir  | 删除一个目录     | rmdir dirname    |
| mvdir  | 移动或重命名一个目录 | mvdir dir1 dir2  |
| cd     | 改变当前目录     | cd dirname       |
| pwd    | 显示当前目录的路径名 | pwd              |
| ls     | 显示当前目录的内容  | ls -la           |
| dircmp | 比较两个目录的内容  | dircmp dir1 dir2 |



## 文件操作

| 命令名  | 功能描述        | 使用举例                      |
| ---- | ----------- | ------------------------- |
| cat  | 显示或连接文件     | cat filename              |
| pg   | 分页格式化显示文件内容 | pg filename               |
| more | 分屏显示文件内容    | more filename             |
| od   | 显示非文本文件的内容  | od -c filename            |
| cp   | 复制文件或目录     | cp file1 file2            |
| rm   | 删除文件或目录     | rm filename               |
| mv   | 改变文件名或所在目录  | mv file1 file2            |
| ln   | 联接文件        | ln -s file1 file2         |
| find | 使用匹配表达式查找文件 | find . -name "*.c" -print |
| file | 显示文件类型      | file filename             |
| open | 使用默认的程序打开文件 | open filename             |