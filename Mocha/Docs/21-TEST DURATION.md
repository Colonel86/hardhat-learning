许多报告器会显示测试持续时间并标记缓慢的测试（默认值：75 毫秒），如 SPEC 报告器所示：

测试持续时间分为三个级别（如下图所示）：

FAST：在“slow”阈值的一半内运行的测试将以绿色显示持续时间（如果有的话）。
NORMAL：运行超过阈值一半（但仍在阈值内）的测试将以黄色显示持续时间。
SLOW：运行超过阈值的测试将以红色显示持续时间。

要调整什么被认为是“slow”，您可以使用 slow() 方法：
```
describe('something slow', function () {
  this.slow(300000); // five minutes

  it('should take long enough for me to go make a sandwich', function () {
    // ...
  });
});
```

