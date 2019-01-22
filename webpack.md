# webpack source code analysis

I decide to analyse the source code of webpack because that i was in trouble when i created a vue app with webpack several weeks ago, the content below is my analysis.

## webpack: Function

```js
const webpack = (options, callback) => {
  // Validate options
  ...
  ...
  // Judge the options is single or multi
  ...
  ...
  // I just analyse the situation when options is single 
  let compiler;
  compiler = new Compiler(options.context);
  if (callback) {
    // In watch mode
    if (option.watch === true) {
      return compiler.watch(watchOptions, callback);
    }
    // Not in watch mode
    compiler.run(callback);
  }
  return compiler;
};
```
So, you can think that the excution of webpack function just inits the options and then return a Compiler instance.

### Compiler: Class

```js
class Compiler extends Tapable {
  constructor(context) {
    super();
    this.hooks = {
      // Some Hook instances
    };
    this.context = context;
  };
  run(callback) {}
  compile(callback) {}
  newCompilation(params) {}
};
```
After we new a Compiler instance in webpack function, we will excute compiler.run(callback). Let's to see the implementation of compiler.run method.
#### run: method

```js
run(callback) {
  // Cxucte beforeRun hook and run hook
  ...
  ...
  this.compile(onCompiled);
};
```
At last, excute this.compile(onCompiled) to start compiling
#### compile: method
```js
compile(callback) {
  // Keep some var in params eg. options.module, options.context
  const params = this.newCompilationParams();
  // Excute beforeCompile hook and compile hook
  ...
  ...
  const compilation = this.newCompilation(params);
  // Excute make hook
  ...
  ...
  compilation.finish();
  compilation.seal(err => {
    if (err) return callback(err);

    this.hooks.afterCompile.callAsync(compilation, err => {
      if (err) return callback(err);

      return callback(null, compilation);
    });
  });
};
```
In fact, compile generate a compilation by excuting this.newCompilation(params), then compilation finish the rest work.
#### newCompilation: method
```js
newCompilation(params) {
  // Get a Compilation instance
  const compilation = this.createCompilation();
  compilation.fileTimestamps = this.fileTimestamps;
  compilation.contextTimestamps = this.contextTimestamps;
  compilation.name = this.name;
  compilation.records = this.records;
  compilation.compilationDependencies = params.compilationDependencies;
  // Exucte thisCompilation hook and compilation hook
  this.hooks.thisCompilation.call(compilation, params);
  this.hooks.compilation.call(compilation, params);
  return compilation;
}
We can get that newCompilation is used to init compilation
```
### Compilation: Class
```js
class Compilation extends Tapable {
  constructor(compiler) {
    super();
    this.hooks = {
      // Some Hook instances
    };
    this.compiler = compiler;
  };
  finish() {}
  seal(callback) {}
}
```

#### finish: method
```js
finish() {
  // Report dependency errors and warnings
}
```

#### seal: method
```js
seal(callback) {
  // Some hooks
  ...
  ...
  for (const preparedEntrypoint of this._preparedEntrypoints) {
    const module = preparedEntrypoint.module;
    const name = preparedEntrypoint.name;
    const chunk = this.addChunk(name);
    const entrypoint = new Entrypoint(name);
    entrypoint.setRuntimeChunk(chunk);
    entrypoint.addOrigin(null, name, preparedEntrypoint.request);
    this.namedChunkGroups.set(name, entrypoint);
    this.entrypoints.set(name, entrypoint);
    this.chunkGroups.push(entrypoint);

    GraphHelpers.connectChunkGroupAndChunk(entrypoint, chunk);
    GraphHelpers.connectChunkAndModule(chunk, module);

    chunk.entryModule = module;
    chunk.name = name;

    this.assignDepth(module);
  }
  this.processDependenciesBlocksForChunkGroups(this.chunkGroups.slice());
  this.sortModules(this.modules);
  // Some hooks
  ...
  ...
  this.applyModuleIds();
  // Some hooks
  ...
  ...
  this.sortItemsWithModuleIds();
  // Some hooks
  ...
  ...
  this.applyChunkIds();
  // Some hooks
  ...
  ...
  this.sortItemsWithChunkIds();
  // Some hooks
  ...
  ...
  this.createHash();
  // Some hooks
  ...
  ...
  this.createModuleAssets();
  // Some hooks
  ...
  ...
  this.createChunkAssets();
  // Some hooks
  ...
  ...
  this.summarizeDependencies();
  // Some hooks
  ...
  ...
}
```
