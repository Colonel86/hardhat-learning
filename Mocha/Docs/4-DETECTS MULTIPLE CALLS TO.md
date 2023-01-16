如果你使用基于回调的异步测试，如果 done() 被多次调用，Mocha 将抛出一个错误。 这对于捕获意外的双重回调很方便。
```
it('double done', function (done) {
  // Calling `done()` twice is an error
  setImmediate(done);
  setImmediate(done);
});
```

运行上面的测试会给你下面的错误信息：
```
  Error: done() called multiple times in test <Double done call done> of file /Users/gengming/BlockChain/hardhat-learning/Mocha/Practices/test/test.js
```