# 指令记录
- 删除所有namespace的配置
```
ip -all netns delete
```

- 两台机器之间，第一次使用sshpass的时候，需要加上以下选项
```
sshpass -p "$passwd" ssh root@x.x.x.x -o StrictHostKeyChecking=no
```

- vim替换指令
```
:s/<find>/<replace>/<flag>

eg:
:%s/foo/bar/g #(%)全局查找，(g)所有出现替换
:s/foo/bar/g #当前行查找替换

<flag>:
g: global
c: confirm each substitution
e: do not show errors
i: ignore case
I: case-senstitive
```

- shell脚本，获取拼接成的变量名的值
```
eval client=\$client${i}
```
