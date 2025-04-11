# 自定义 webpack plugin

Webpack 启动后，在读取配置的过程中会先执行 `new OSSUploadPlugin({...})` 初始化一个 `OSSUploadPlugin` 并获得其实例。在初始化 `Compiler` 对象后，再调用 `OSSUploadPlugin.apply(compiler)` 为插件实例传入 `compiler` 对象。插件实例在获取到 `compiler` 对象后，就可以通过 `compiler.plugin(事件名称 ，回调函数)` 监听到 Webpack 广播的事件，并且可以通过 `compiler` 对象去操作 Webpack 。

```js
class OSSUploadPlugin {
  // 在构造函数中获取用户为该插件传入的配置
  constructor(options) {
    this.options = options;
    // .............
  }

  // ............

  // 原型定义一个 apply 函数，并注入了 compiler 对象
  apply(compiler) {
    // 绑定异步钩子事件，afterEmit 输出 asset 到 output 目录之后执行
    compiler.hooks.afterEmit.tapAsync(
      "OSSUploadPlugin",
      (compilation, callback) => {
        const folderPath = this.options.folderPath;
        this.readAndUploadPics(folderPath);
        // 执行 callback 回调
        callback();
      }
    );
  }
}

module.exports = OSSUploadPlugin;
```

上述代码块编写了一个叫 OSSUploadPlugin 的类，它提供了一个叫 apply 的方法，在该方法中可以从外部获取到 Webpack 执行全过程中单一的 compiler 实例，通过 compiler 实例，可以在 Webpack 的生命周期的 afterEmit 节点 tap 一个监听事件。

## Compiler、Compilation

Compiler 对象就是 webpack 的实体（是 Tapable 的实例），掌控整个 webpack 的生命周期，他不执行具体的任务，只是进行一些调度工作（调兵遣将）。他创建了 Compilation 对象， Compilation 任务执行完毕后会将最终的处理结果返回给 Compiler 。

Compilation 是编译阶段的主要执行者，（是 Tapable 的实例），执行模块创建、依赖收集、分块、打包等主要任务的对象。

###

| CompilerHook         | 触发时间                                                         | 适用情况及实际功能需求                                                                                  |
| -------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| entryOption          | 在入口选项处理完成后触发。通常用于修改或添加入口配置。           | 需要自定义入口配置或添加新的入口点时。例如，在构建多页面应用时，动态添加新的入口点。                    |
| afterPlugins         | 在所有插件加载完成后触发。可以用于插件间的交互或调整插件顺序。   | 需要在插件加载完成后进行插件间的协调或调整插件执行顺序时。例如，确保某个插件在另一个插件之后执行。      |
| afterResolvers       | 在解析器（resolvers）设置完成后触发。用于自定义模块解析逻辑。    | 需要修改或扩展模块解析逻辑时。例如，自定义模块路径解析规则，添加别名解析。                              |
| beforeRun            | 在编译器运行之前触发。通常用于执行一些初始化操作。               | 需要在编译器启动之前执行一些前置操作时，如清理缓存、初始化日志系统或设置环境变量。                      |
| run                  | 在编译器开始编译之前触发。可以在此钩子中添加自定义的编译前逻辑。 | 需要在每次编译开始之前执行操作时。例如，编译前检查代码风格或执行预处理脚本。                            |
| normalModuleFactory  | 在创建普通模块工厂时触发。用于自定义模块创建逻辑。               | 需要自定义模块创建过程时。例如，修改模块的创建参数或添加自定义的模块处理逻辑。                          |
| contextModuleFactory | 在创建上下文模块工厂时触发。用于自定义上下文模块创建逻辑。       | 需要自定义上下文模块创建过程时。例如，定义上下文模块的解析规则或添加新的上下文模块类型。                |
| beforeCompile        | 在编译开始之前触发。用于准备编译工作。                           | 需要在实际编译开始之前做一些准备工作时。例如，生成动态配置文件或预处理资源。                            |
| compile              | 在编译器开始读取并解析所有模块之前触发。                         | 需要在编译阶段开始时做一些操作时。例如，启动编译进度条或记录编译开始时间。                              |
| thisCompilation      | 在创建一个新的 compilation 对象时触发。                          | 需要在每次新的 compilation 对象创建时执行操作时。例如，添加自定义插件到 compilation 对象中。            |
| compilation          | 在 compilation 对象创建后触发。                                  | 需要对 compilation 对象进行操作或添加插件时。例如，在 compilation 对象中注册新的构建步骤或处理逻辑。    |
| make                 | 在编译模块时触发。通常用于修改模块的编译流程。                   | 需要在模块编译过程中插入自定义逻辑时。例如，动态添加依赖模块或修改模块的编译选项。                      |
| afterCompile         | 在编译完成后触发。用于处理编译结果。                             | 需要在编译结束后处理编译结果或做一些后续操作时。例如，分析编译输出或记录编译日志。                      |
| shouldEmit           | 在生成输出文件之前触发。用于决定是否生成输出文件。               | 需要在决定是否生成输出文件之前插入逻辑时。例如，条件性地生成某些文件或跳过生成特定文件。                |
| emit                 | 在输出文件生成之前触发。可以在此钩子中修改输出文件。             | 需要在输出文件生成之前修改、添加或删除文件内容时。例如，插入版权声明、添加额外文件或修改文件名。        |
| afterEmit            | 在输出文件生成后触发。用于执行生成后的清理或其他操作。           | 需要在输出文件生成后执行操作时。例如，发送构建完成通知、运行后续脚本或进行清理工作。                    |
| done                 | 在编译完成后触发。用于执行编译完成后的操作。                     | 需要在编译整个过程完成后执行操作时。例如，打印编译统计信息或触发后续构建流程。                          |
| additionalPass       | 在编译器进行额外的 pass 时触发。                                 | 需要在编译器进行额外的编译 pass 时执行操作时。例如，处理额外的文件或重新编译部分模块。                  |
| beforeResolvers      | 在解析器设置之前触发。用于自定义解析逻辑。                       | 需要在解析器设置之前自定义解析逻辑时。例如，添加自定义模块解析规则或扩展解析器功能。                    |
| invalid              | 在文件无效时触发（例如在 watch 模式下文件修改）。                | 需要在文件被标记为无效时（如在 watch 模式下文件发生变化）执行操作时。例如，记录文件变更或触发增量编译。 |
| watchRun             | 在 watch 模式下编译器开始运行时触发。                            | 需要在 watch 模式下每次编译开始前执行操作时。例如，初始化 watch 模式相关的资源或设置监控机制。          |
| failed               | 在编译失败时触发。                                               | 需要在编译过程发生错误时执行操作时。例如，记录错误日志或发送错误通知。                                  |
| environment          | 在编译器环境设置完成时触发。                                     | 需要在编译器环境设置完成后执行初始化操作时。例如，设置编译器环境变量或加载自定义配置。                  |
| afterEnvironment     | 在编译器环境设置之后触发。                                       | 需要在编译器环境设置完成后立即执行操作时。例如，初始化插件或进行环境检查。                              |

## Tapable

Webpack 本质上是一种事件流的机制，它的工作流程就是将各个插件串联起来，而实现这一切的核心就是 Tapable 。

## tapAsync

注册事件回调有三个方法： tap 、 tapAsync 和 tapPromise ，其中 tapAsync 和 tapPromise 不能用于 Sync 开头的钩子类，强行使用会报错。相比于 tap，tapAsync 需要执行 callback 函数才能确保流程会走到下一个插件中去。 call 对应 tap ， callAsync 对应 tapAsync ， promise 对应 tapPromise 。

```js
myCar.hooks.calculateRoutes.tapAsync(
  "BingMapsPlugin",
  (source, target, routesList, callback) => {
    bing.findRoute(source, target, (err, route) => {
      if (err) return callback(err);
      routesList.add(route);
      // call the callback
      callback();
    });
  }
);
```
