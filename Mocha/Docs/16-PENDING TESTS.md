"Pending"——如“最终应该有人编写这些测试用例”——测试用例是那些没有回调的：
```
describe('Array', function () {
  describe('#indexOf()', function () {
    // pending test below
    it('should return -1 when the value is not present');
  });
});
```

待定测试将包含在测试结果中，并标记为待定。 挂起的测试不被视为失败的测试。

阅读[包容性测试部分](https://mochajs.org/#inclusive-tests)，了解通过 this.skip() 有条件地将测试标记为挂起的示例。