#### SUITE-LEVEL
套件级超时可以应用于整个测试“套件”，或通过 this.timeout(0) 禁用。 这将被所有不覆盖该值的嵌套套件和测试用例继承。
```
describe('a suite of tests', function () {
  this.timeout(500);

  it('should take less than 500ms', function (done) {
    setTimeout(done, 300);
  });

  it('should take less than 500ms as well', function (done) {
    setTimeout(done, 250);
  });
});
```

#### TEST-LEVEL
也可以应用特定于测试的超时，或者使用 this.timeout(0) 一起禁用超时：
```
it('should take less than 500ms', function (done) {
  this.timeout(500);
  setTimeout(done, 300);
});
```

#### HOOK-LEVEL
挂钩级超时也可以应用：
```
describe('a suite of tests', function () {
  beforeEach(function (done) {
    this.timeout(3000); // A very long environment setup.
    setTimeout(done, 2500);
  });
});
```
同样，使用 this.timeout(0) 来禁用挂钩的超时。

> 在 v3.0.0 或更新版本中，传递给 this.timeout() 的参数大于最大延迟值将导致超时被禁用。 在 v8.0.0 或更新版本中，this.enableTimeouts() 已被删除。 警告：对于异步测试，如果您通过 this.timeout(0) 禁用超时然后不调用 done()，您的测试将静默退出。