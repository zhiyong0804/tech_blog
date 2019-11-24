# panic&recover&defer

#### panic
* panic一旦发生，控制权就会迅速的沿着调用栈的反方向传播
* panic后面的代码根本没有执行的机会
* 