`mocha` 可执行文件支持`node`可执行文件支持的所有适用标志。

这些标志因您的 Node.js 版本而异。

`node`标志可以在 Mocha 的配置中定义。

`--enable-source-maps`
如果将 `--enable-source-maps` 标志传递给 mocha，源映射将被收集并用于为转译代码提供准确的堆栈跟踪：
```
Error: cool
    at Object.<anonymous> (/Users/fake-user/bigco/nodejs-tasks/build/src/index.js:27:7)
        -> /Users/fake-user/bigco/nodejs-tasks/src/index.ts:24:7
```