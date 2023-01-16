EXCLUSIVE TESTS
> 警告：独占测试与并行模式不兼容。

排他性功能允许您通过将 .only() 附加到函数来仅运行指定的套件或测试用例。 这是仅执行特定套件的示例：
```
describe('Array', function () {
  describe.only('#indexOf()', function () {
    // ...
  });
});
```
注意：所有嵌套套件仍将被执行。

这是执行单个测试用例的示例：
```
describe('Array', function () {
  describe('#indexOf()', function () {
    it.only('should return -1 unless present', function () {
      // ... 只有这个会执行
    });

    it('should return the index when present', function () {
      // ... 这个不会执行
    });
  });
});

```
在 v3.0.0 之前，.only() 使用字符串匹配来决定执行哪些测试； 这已不再是这种情况。 在 v3.0.0 或更新版本中，可以多次使用 .only() 来定义要运行的测试子集：
```
describe('Array', function () {
  describe('#indexOf()', function () {
    it.only('should return -1 unless present', function () {
      // this test will be run 这个会执行
    });

    it.only('should return the index when present', function () {
      // this test will also be run 这个会执行
    }); 

    it('should return -1 if called with a non-Array context', function () {
      // this test will not be run 这个不会执行
    });
  });
});
```
您也可以选择多个suites：
```
describe('Array', function () {
  describe.only('#indexOf()', function () { //这个会执行
    it('should return -1 unless present', function () {
      // this test will be run
    });

    it('should return the index when present', function () {
      // this test will also be run
    });
  });

  describe.only('#concat()', function () { //这个会执行
    it('should return a new Array', function () {
      // this test will also be run
    });
  });

  describe('#slice()', function () {  //这个不会执行
    it('should return a new Array', function () {
      // this test will not be run
    });
  });
});
```
但测试将具有优先权：
```
describe('Array', function () {
  describe.only('#indexOf()', function () {
    it.only('should return -1 unless present', function () {
      // this test will be run
    });

    it('should return the index when present', function () {
      // this test will not be run
    });
  });
});
```
> 注意：挂钩（如果存在）仍将被执行。

> 请注意不要将 .only() 的用法提交给版本控制，除非你真的是认真的！ 为此，可以在持续集成测试命令（或 git 预提交挂钩）中使用 --forbid-only 选项运行 mocha。