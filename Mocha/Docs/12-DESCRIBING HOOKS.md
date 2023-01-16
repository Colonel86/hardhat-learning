可以使用可选描述调用任何挂钩，从而更容易查明测试中的错误。 如果一个钩子被赋予了一个命名函数，那么在没有提供描述的情况下将使用该名称。

```
beforeEach(function () {
  // beforeEach hook
});

beforeEach(function namedFun() {
  // beforeEach:namedFun
});

beforeEach('some description', function () {
  // beforeEach:some description
});
```

