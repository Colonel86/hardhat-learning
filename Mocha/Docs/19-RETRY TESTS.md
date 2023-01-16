您可以选择最多重试失败的测试一定次数。 此功能旨在处理无法轻松mocked/stubbed资源的端到端测试（功能测试/Selenium ...）。 不建议将此功能用于单元测试。

此功能确实会重新运行失败的测试及其相应的 `beforeEach/afterEach` 挂钩，但不会重新运行`before/after`的挂钩。 `this.retries()` 对失败的钩子没有影响。

注意：下面的示例是使用 Selenium webdriver 编写的（它覆盖了 Promise 链的全局 Mocha 挂钩）。

```
describe('retries', function () {
  // Retry all tests in this suite up to 4 times
  this.retries(4);

  beforeEach(function () {
    browser.get('http://www.yahoo.com');
  });

  it('should succeed on the 3rd try', function () {
    // Specify this test to only retry up to 2 times
    this.retries(2);
    expect($('.foo').isDisplayed()).to.eventually.be.true;
  });
});
```

