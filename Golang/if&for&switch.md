# if&for&switch

#### range
* range表达式只会在for语句开始执行时被求值一次
* range表达式的求值结果会被复制，被迭代的对象是range表达式结果值的副本

#### switch和case关系
* switch 的case语句在case子句上的选择是唯一的，不允许有case中子表达式和其他case子表达式值想等的情况
* 只要switch表达式的结果值与某个case表达式中的任意一个子表达式的结果值相等，该case表达式所属的case子句就会被选中