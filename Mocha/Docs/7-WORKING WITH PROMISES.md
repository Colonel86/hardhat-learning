## 与promises一起工作
或者，您可以返回一个 Promise，而不是使用 `done()` 回调。 如果您正在测试的 API 返回Promise而不是接受回调，这将很有用：
```
beforeEach(function () {
  return db.clear().then(function () {
    return db.save([tobi, loki, jane]);
  });
});

describe('#find()', function () {
  it('respond with matching records', function () {
    return db.find({type: 'User'}).should.eventually.have.length(3);
  });
});
```
后一个示例使用 [Chai as Promised](https://www.npmjs.com/package/chai-as-promised) 来实现流畅的 promise 断言。

在 Mocha v3.0.0 和更新版本中，返回一个 `Promise` 并调用 `done()` 将导致异常，因为这通常是一个错误：
```
const assert = require('assert');

// antipattern
it('should complete this test', function (done) {
  return new Promise(function (resolve) {
    assert.ok(true);
    resolve();
  }).then(done);
});
```
正确使用方法：
```
it('should complete this test', function () {
    return new Promise(function (resolve) {
      assert.ok(true);
      resolve();
    }).then();
});
it('should complete this test', function (done) {
    return new Promise(function () {
      assert.ok(true);
      done();
    }).then(done);
});
```
上面的测试将失败`Error: Resolution method is overspecified. Specify a callback *or* return a Promise; not both.`

在 v3.0.0 之前的版本中，对 `done()` 的调用实际上被忽略了。

