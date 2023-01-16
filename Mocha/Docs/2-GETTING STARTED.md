```
$ npm install mocha
$ mkdir test
$ $EDITOR test/test.js # or open with your favorite editor
```
在你的编辑器中：
```
var assert = require('assert');
describe('Array', function () {
  describe('#indexOf()', function () {
    it('should return -1 when the value is not present', function () {
      assert.equal([1, 2, 3].indexOf(4), -1);
    });
  });
});
```
回到终端：
```
$ ./node_modules/mocha/bin/mocha.js

  Array
    #indexOf()
      ✓ should return -1 when the value is not present


  1 passing (9ms)
```
在 package.json 中设置测试脚本：
```
"scripts": {
  "test": "mocha"
}
```
然后运行测试：
```
$ npm test
```