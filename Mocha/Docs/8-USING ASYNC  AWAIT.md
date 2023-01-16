如果你的 JS 环境支持 [async/await](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Statements/async_function)，你也可以这样写异步测试：

```
beforeEach(async function () {
  await db.clear();
  await db.save([tobi, loki, jane]);
});

describe('#find()', function () {
  it('responds with matching records', async function () {
    const users = await db.find({type: 'User'});
    users.should.have.length(3);
  });
});
```