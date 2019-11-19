# storm实现在线实时统计wordcount

#### 思路

1. 使用 spout 读取文件，然后将文件里面的单词全部读取出来，然后坐上标记，比如说改个后缀，然后发射给 bolt

2. bolt 统计 单词出现的次数，然后输出

#### 代码实例

https://github.com/winterme/storm-study/blob/master/src/main/java/com/wordcount/topology/WordCountMain.java