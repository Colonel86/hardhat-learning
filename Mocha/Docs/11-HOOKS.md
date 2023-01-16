凭借其默认的“BDD”风格接口，Mocha 提供了 before()、after()、beforeEach() 和 afterEach() 钩子。 这些应该用于设置先决条件并在测试后进行清理。

```
describe('hooks', function () {
  before(function () {
    // runs once before the first test in this block
  });

  after(function () {
    // runs once after the last test in this block
  });

  beforeEach(function () {
    // runs before each test in this block
  });

  afterEach(function () {
    // runs after each test in this block
  });

  // test cases
});
```
测试可以出现在你的钩子之前、之后或穿插在你的钩子之间。 钩子将按照它们定义的顺序运行； 所有 `before()` 挂钩运行（一次），然后是任何 `beforeEach()` 挂钩、测试、任何 `afterEach()` 挂钩，最后是 `after()` 挂钩（一次）。

