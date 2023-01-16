此功能与 .only() 相反。 通过附加 .skip()，您可以告诉 Mocha 忽略测试用例。 跳过的任何内容都将被标记为待处理，并据此进行报告。 以下是跳过单个测试的示例：
```
describe('Array', function () {
  describe('#indexOf()', function () {
    it.skip('should return -1 unless present', function () {
      // this test will not be run
    });

    it('should return the index when present', function () {
      // this test will be run
    });
  });
});
```

您还可以将 .skip() 放在整个suite中。 这相当于将 .skip() 附加到suite中的所有测试。 suite中的挂钩也被跳过。
```
describe('Array', function () {
  describe.skip('#indexOf()', function () {
    it('should return -1 unless present', function () {
      // this test will not be run
    });
  });
});
```
> suite 指 describe

注意：跳过套件中的代码，即放置在挂钩或测试之外的代码仍会执行，因为 mocha 仍会调用套件函数来构建套件结构以进行可视化。

> 最佳实践：使用 .skip() 而不是注释掉测试。

您也可以使用 this.skip() 在运行时跳过。 如果测试需要事先无法检测到的环境或配置，则运行时跳过是合适的。 例如：
```
it('should only test in the correct environment', function() {
  if (/* check test environment */) {
    // make assertions
  } else {
    this.skip();
  }
});
```
上述测试将被报告为pending。 同样重要的是要注意调用 this.skip() 将有效地中止测试。
> 最佳实践：为避免混淆，在调用 this.skip() 后不要在测试或挂钩中执行进一步的指令。

将上面的测试与以下代码进行对比：
```
it('should only test in the correct environment', function() {
  if (/* check test environment */) {
    // make assertions
  } else {
    // do nothing
  }
});
```
因为这个测试什么都不做，所以它会被报告为通过。
> 最佳实践：不要什么都不做！ 测试应该做出断言或使用 this.skip()。

要以这种方式跳过多个测试，请在“before all”挂钩中使用 this.skip() ：
```
before(function() {
  if (/* check test environment */) {
    // setup code
  } else {
    this.skip();
  }
});
```
这将跳过所有`it`，`beforeEach/afterEach`，和`describe`套件中的块。 `before/after` 钩子被跳过，除非它们被定义在与包含 `this.skip()` 的钩子相同的级别。
```
describe('outer', function () {
  before(function () {
    this.skip();
  });

  after(function () {
    // will be executed
  });

  describe('inner', function () {
    before(function () {
      // will be skipped
    });

    after(function () {
      // will be skipped
    });
  });
});
```
> 在 v7.0.0 中更新：在“after all”挂钩中跳过测试是不允许的，并且会抛出异常。 使用 return 语句或其他方式中止挂钩执行。

