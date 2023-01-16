警告：延迟的root suites与并行模式不兼容。

如果您需要在任何套件运行之前执行异步操作（例如，动态生成测试），您可能会延迟根套件。 使用 --delay 标志运行 mocha。 这会将一个特殊的回调函数 run() 附加到全局上下文：
```
const assert = require('assert');

const fn = async x => {
  return new Promise(resolve => {
    setTimeout(resolve, 3000, 2 * x);
  });
};

// instead of an IIFE, you can use 'setImmediate' or 'nextTick' or 'setTimeout'
(async function () {
  const z = await fn(3);

  describe('my suite', function () {
    it(`expected value ${z}`, function () {
      assert.strictEqual(z, 6);
    });
  });

  run();
})();
```