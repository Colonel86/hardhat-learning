## 异步代码
通过将参数（通常命名为 `done`）添加到 `it()` 到测试回调，Mocha 将知道它应该等待调用此函数以完成测试。 此回调接受 `Error` 实例（或其子类）或虚假值； 其他任何东西都是无效的用法并会引发错误（通常会导致测试失败）。
```
describe('User', function () {
  describe('#save()', function () {
    it('should save without error', function (done) {
      var user = new User('Luna');
      user.save(function (err) {
        if (err) done(err);
        else done();
      });
    });
  });
});
```
或者，直接使用 `done()` 回调（如果存在错误参数，它将处理错误参数）：
```
describe('User', function () {
  describe('#save()', function () {
    it('should save without error', function (done) {
      var user = new User('Luna');
      user.save(done);
    });
  });
});
```

