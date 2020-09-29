##Git
####1.日常报错
>> 1.如何去解决fatal: refusing to merge unrelated histories
```.java
  这是因为远程仓库已经存在代码记录了，并且那部分代码没有和本地仓库进行关联，
  我们可以使用如下操作允许pull未关联的远程仓库旧代码：
  git pull origin master --allow-unrelated-histories
  然后我们可以push代码了
```

>>2

