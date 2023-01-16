## 动态生成测试
鉴于 Mocha 使用函数表达式来定义套件和测试用例，动态生成测试很简单。 不需要特殊的语法——可以使用普通的 JavaScript 来实现类似于“参数化”测试的功能，您可能已经在其他框架中看到过这种功能。

举个例子：
```
const assert = require('assert');

function add(args) {
  return args.reduce((prev, curr) => prev + curr, 0);
}

describe('add()', function () {
  const tests = [
    {args: [1, 2], expected: 3},
    {args: [1, 2, 3], expected: 6},
    {args: [1, 2, 3, 4], expected: 10}
  ];

  tests.forEach(({args, expected}) => {
    it(`correctly adds ${args.length} args`, function () {
      const res = add(args);
      assert.strictEqual(res, expected);
    });
  });
});
```
上面的代码将生成一个包含三个规格的套件：
```
$ mocha

  add()
    ✓ correctly adds 2 args
    ✓ correctly adds 3 args
    ✓ correctly adds 4 args
```

在 .forEach 处理程序中添加的测试通常不能很好地与编辑器插件一起使用，尤其是“right-click run”功能。 另一种参数化测试的方法是用闭包生成它们。 下面的例子等同于上面的例子：
```
describe('add()', function () {
  const testAdd = ({args, expected}) =>
    function () {
      const res = add(args);
      assert.strictEqual(res, expected);
    };

  it('correctly adds 2 args', testAdd({args: [1, 2], expected: 3}));
  it('correctly adds 3 args', testAdd({args: [1, 2, 3], expected: 6}));
  it('correctly adds 4 args', testAdd({args: [1, 2, 3, 4], expected: 10}));
});
```

使用`top-level await`，您可以在加载测试文件时以动态和异步的方式收集测试数据。
对于没有`top-level await`的 CommonJS 模块，另请参阅 [--delay](https://mochajs.org/#delayed-root-suite)。

```
// testfile.mjs
import assert from 'assert';

// top-level await: Node >= v14.8.0 with ESM test file
const tests = await new Promise(resolve => {
  setTimeout(resolve, 5000, [
    {args: [1, 2], expected: 3},
    {args: [1, 2, 3], expected: 6},
    {args: [1, 2, 3, 4], expected: 10}
  ]);
});

// in suites ASYNCHRONOUS callbacks are NOT supported
describe('add()', function () {
  tests.forEach(({args, expected}) => {
    it(`correctly adds ${args.length} args`, function () {
      const res = args.reduce((sum, curr) => sum + curr, 0);
      assert.strictEqual(res, expected);
    });
  });
});
```

