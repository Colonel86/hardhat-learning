所有挂钩（before()、after()、beforeEach()、afterEach()）都可以是同步的或异步的，表现得非常像常规测试用例。 例如，您可能希望在每次测试之前用虚拟内容填充数据库：

```
describe('Connection', function () {
  var db = new Connection(),
    tobi = new User('tobi'),
    loki = new User('loki'),
    jane = new User('jane');

  beforeEach(function (done) {
    db.clear(function (err) {
      if (err) return done(err);
      db.save([tobi, loki, jane], done);
    });
  });

  describe('#find()', function () {
    it('respond with matching records', function (done) {
      db.find({type: 'User'}, function (err, res) {
        if (err) return done(err);
        res.should.have.length(3);
        done();
      });
    });
  });
});
```
