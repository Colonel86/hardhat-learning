不鼓励将[arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)（又名“lambdas”）传递给 Mocha。 Lambda 词法绑定 this 并且无法访问 Mocha 上下文。 例如，以下代码将失败：
```
describe('my suite', () => {
  it('my test', () => {
    // should set the timeout of this test to 1000 ms; instead will fail
    this.timeout(1000);
    assert.ok(true);
  });
});
```
如果您不需要使用 Mocha 的上下文，lambda 应该可以。 请注意，如果最终需要，使用 lambda 进行重构会更加痛苦！

