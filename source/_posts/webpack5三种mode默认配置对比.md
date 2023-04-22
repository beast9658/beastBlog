---
title: webpack5三种mode默认配置对比
date: 2022-10-21 22:49:27
---
虽然[webpack的文档](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Fmode%2F "https://webpack.js.org/configuration/mode/")中已经给出了三种模式默认的配置项的配置，但是我还是好奇除了这些列出的配置，是否还有其他的配置之间的差异呢？

## 导出三种模式的配置对象

webpack的版本是V5.36.1，获取默认配置的源码路径为`webpack/lib/WebpackOptionsDefaulter.js` ，该文件源码如下：

```
const { applyWebpackOptionsDefaults } = require("./config/defaults");
const { getNormalizedWebpackOptions } = require("./config/normalization");

class WebpackOptionsDefaulter {
	process(options) {
		options = getNormalizedWebpackOptions(options);
		applyWebpackOptionsDefaults(options);
		return options;
	}
}

module.exports = WebpackOptionsDefaulter;
复制代码
```

我们的目的是获取每个模式的options，所以暂且不需要管这里引入的两个模块的具体代码，通过这段代码得知，只需要通过`WebpackOptionsDefaulter` 的实例方法process，即可获取每种模式的具体配置，获取默认配置代码如下：

```
// getWebpackConfig.js
const fs = require('fs');
const {stringify} = require('javascript-stringify');
const WebpackDefaulterClass = require('webpack/lib/WebpackOptionsDefaulter');
const webpackDefaulter = new WebpackDefaulterClass();

['none','development','production'].forEach(mode => generateOptions(mode));

function generateOptions(mode) {
  const options = webpackDefaulter.process({mode});
  fs.writeFileSync(`./webpack.${mode}.config.js`, 'module.exports = ' + stringify(options));
}
复制代码
```

在命令行执行`node getWebpackConfig` 之后，就把相对应的配置输出到对应的文件中，再对文件进行代码格式化，准备工作就好了。

## 三种模式的配置差异

### 1、[cache](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Fother-options%2F%23cache "https://webpack.js.org/configuration/other-options/#cache")

-   production模式为`false`

-   development模式为

    ```
    {
      cache: {
        type: 'memory',
        maxGenerations: Infinity
      }
    }
    复制代码
    ```

-   none模式为`false`

cache配置的作用是换成已经处理过的module和chunk，以达到加速构建的目的。production模式一般为全新构建打包，所以默认为不使用缓存，但是在开发过程中，构建速度直接影响到开发效率，所以development模式下，webpack会使用缓存。

type支持配置为filesystem和memory，即文件系统和内存缓存，开发模式下追求快速，所以配置为memory。

maxGenerations是5.30.0+版本才有的配置，该配置设置没有被使用的内存缓存的寿命，配置值类型为number，设置为Infinity表示一直保留。

### 2、[devtool](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Fdevtool%2F%23root "https://webpack.js.org/configuration/devtool/#root")

-   production模式为`false`

-   none模式为`false`

-   development中的配置为：

    ```
    {
      devtool: 'eval'
    }
    复制代码
    ```

该配置指定source map的格式，false则不生成source map，development模式默认使用eval格式，eval格式的特点每个模块都使用 `eval()` 执行，并且都有 `//@ sourceURL`。此选项会非常快地构建。主要缺点是，由于会映射到转换后的代码，而不是映射到原始代码（没有从 loader 中获取 source map），所以不能正确的显示行数。

如果构建速度要求没那么高时，可以使用eval-cheap-module-source-map替代eval，eval-cheap-module-source-map会牺牲一定的构建速度，但是可以提供原始代码行数的映射。

### 3、[module.unsafeCache](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Fmodule%2F "https://webpack.js.org/configuration/module/")

-   production模式为`false`

-   none模式为`false`

-   development的配置为：

    ```
    // const NODE_MODULES_REGEXP = /[\/]node_modules[\/]/i;
    {
      unsafeCache: module => {
        const name = module.nameForCondition();
        return name && NODE_MODULES_REGEXP.test(name);
      },
    }
    复制代码
    ```

该配置表示是否缓存模块请求的解析，development的unsafeCache配置表示，如果通过模块的nameForCondition方法得到的模块路径存在，且是node_modules中的模块，则缓存。

### 4、[optimization.splitChunks.hidePathInfo](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fplugins%2Fsplit-chunks-plugin%2F%23splitchunkshidepathinfo "https://webpack.js.org/plugins/split-chunks-plugin/#splitchunkshidepathinfo")

该配置表示由于`optimization.splitChunks.maxSize`配置而创建的提取的文件名称隐藏路径信息，表现为派生名称是模块名称还是哈希值。

-   production模式为`true`
-   development模式为`false`
-   none模式为`false`

当 chunk 已经有一个名称时，每个部分将获得一个从该名称派生的新名称。 根据 `optimization.splitChunks.hidePathInfo` 的值，它将添加一个从第一个模块名称或其哈希值派生的密钥。

### 5、[optimization.splitChunks.minRemainingSize](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fplugins%2Fsplit-chunks-plugin%2F%23splitchunksminremainingsize "https://webpack.js.org/plugins/split-chunks-plugin/#splitchunksminremainingsize")

-   production模式为`undefined`（此时使用的是`splitChunks.minSize`的值）
-   development模式为`0`
-   none模式为`undefined`（此时使用的是`splitChunks.minSize`的值）

webpack 5中引入了`splitChunks.minRemainingSize`选项，通过确保拆分后剩余的最小块大小大于限制来避免大小为零的模块。开发模式下默认为0。在其他情况下，`splitChunks.minRemainingSize`的默认值为`splitChunks.minSize`的值，因此除极少数需要过度干预的情况外，无需手动指定它。

### 6、[optimization.splitChunks.enforceSizeThreshold](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fplugins%2Fsplit-chunks-plugin%2F%23splitchunksenforcesizethreshold "https://webpack.js.org/plugins/split-chunks-plugin/#splitchunksenforcesizethreshold")

-   production模式为`5000`
-   development模式为`3000`
-   none模式为`3000`

### 7、[optimization.emitOnErrors](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationemitonerrors "https://webpack.js.org/configuration/optimization/#optimizationemitonerrors")

-   prodution模式为`false`
-   development模式为`true`
-   none模式为`true`

该配置表示在编译的过程中遇到错误时，是否依然生成资源，所以当生产构建过程中出现错误会终止编译，而开发时不会终止，但是会在代码运行时出现报错。

### 8、[optimization.flagIncludedChunks](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationflagincludedchunks "https://webpack.js.org/configuration/optimization/#optimizationflagincludedchunks")

-   prodution模式为`true`
-   development模式为`false`
-   none模式为`false`

设置为true时，webpack标记作为其他chunk子集的chunk，在已经加载过较大的chunk时不必加载子集chunk。

### 9、[optimization.moduleIds和optimization.chunkIds](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationmoduleids "https://webpack.js.org/configuration/optimization/#optimizationmoduleids")

两个配置分别为模块id和chunk id选择某种名称算法，这个配置三种模式都不相同。

-   production模式下都为`deterministic`，模块名称被散列为较小的数值。
-   development模式下都为`named`，模块名称可读性强，方便调试。
-   none模式下都为`natural`，模块名称是按使用顺序的数字ID。

### 10、[optimization.sideEffects](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationsideeffects "https://webpack.js.org/configuration/optimization/#optimizationsideeffects")

-   production模式为`true`
-   development模式为`flag`
-   none模式为`flag`

告知 webpack 去辨识 package.json 中的 副作用 标记或规则，以跳过那些当导出不被使用且被标记不包含副作用的模块。

### 11、[optimization.usedExports](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationusedexports "https://webpack.js.org/configuration/optimization/#optimizationusedexports")

-   production模式为`true`
-   development模式为`false`
-   none模式为`false`

它告诉Webpack去决定每一个模块所用到的导出。有了它，Webpack会在打包产出里添加额外的像是/* unused harmony export */之类的注释，在后续的压缩步骤中可用到。

### 12、[optimization.innerGraph](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationinnergraph "https://webpack.js.org/configuration/optimization/#optimizationinnergraph")

告知 webpack 是否对未使用的导出内容，实施内部图形分析。

-   production模式为`true`
-   development模式为`false`
-   none模式为`false`

### 13、[optimization.mangleExports](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationmangleexports "https://webpack.js.org/configuration/optimization/#optimizationmangleexports")

该配置为是否允许控制导出处理。

-   production模式为`true`，等价于`deterministic`，简写形式 - 通常两个字符 — 在添加或移除 export 时不会改变。适用于长效缓存。
-   development模式为`false`，保留原名，有利于阅读和调试。
-   none模式为`false`，保留原名，有利于阅读和调试。

### 14、[optimization.concatenateModules](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationconcatenatemodules "https://webpack.js.org/configuration/optimization/#optimizationconcatenatemodules")

该配置告知webpack是否根据模块图将模块安全地合并为一个模块。

-   production模式为`true`
-   development模式为`false`
-   none模式为`false`

### 15、[optimization.realContentHash](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationrealcontenthash "https://webpack.js.org/configuration/optimization/#optimizationrealcontenthash")

资源生成并生成正确的资源内容hash后，再添加一个额外的hash。

-   production模式为`true`
-   development模式为`false`
-   none模式为`false`

### 16、[optimization.minimize](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationminimize "https://webpack.js.org/configuration/optimization/#optimizationminimize")

告诉 webpack使用 [TerserPlugin](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fplugins%2Fterser-webpack-plugin%2F "https://webpack.js.org/plugins/terser-webpack-plugin/") 或者在 [`optimization.minimizer`](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Foptimization%2F%23optimizationminimizer "https://webpack.js.org/configuration/optimization/#optimizationminimizer")选项中配置的插件压缩bundle。

-   production模式下为`true`
-   development模式为`false`
-   none模式为`false`

三种模式下的`optimization.minimizer`配置都是一样的：

```
minimizer: [{
  apply: compiler => {
    // Lazy load the Terser plugin
    const TerserPlugin = require("terser-webpack-plugin");
    new TerserPlugin({
      terserOptions: {
        compress: {
          passes: 2
        }
      }
    }).apply(compiler);
  }
}],
复制代码
```

### 17、optimization.nodeEnv

该配置会将值赋值给`process.env.NODE_ENV`，如果是false，则不赋值。

-   production模式为`production`
-   development模式为`development`
-   none模式为`false`

### 18、[performance](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Fperformance%2F "https://webpack.js.org/configuration/performance/")

通过该配置可以设置当构建资源超出限制时如何提示。

-   production模式配置为：

    ```
    performance: {
      maxAssetSize: 250000, // 最大资源大小250KB
      maxEntrypointSize: 250000, // 最大入口资源大小250KB
      hints: 'warning' // 超出限制时只给出警告
    },
    复制代码
    ```

-   development模式为`false`

-   none模式为`false`

## 三种模式目标

从以上分析的差异可以再次验证三种模式的目标的差异。

`production`模式是为了生产环境资源访问的效率。

`development`模式是为了开发环境的调试效率。

`none`模式一般都不会用上，它基本上和`development`模式相同，在缓存和开发调试上又有些不足，所以基本上不选择`none`模式。

## 三种模式的配置对象

### `mode: 'none'`

```
module.exports = {
  amd: undefined,
  bail: undefined,
  cache: false,
  context: '/Users/test',
  dependencies: undefined,
  devServer: undefined,
  devtool: false,
  entry: {
    main: {
      'import': ['./src']
    }
  },
  experiments: {
    topLevelAwait: false,
    syncWebAssembly: false,
    asyncWebAssembly: false,
    outputModule: false
  },
  externals: undefined,
  externalsPresets: {
    web: true,
    node: false,
    nwjs: false,
    electron: false,
    electronMain: false,
    electronPreload: false,
    electronRenderer: false
  },
  externalsType: 'var',
  ignoreWarnings: undefined,
  infrastructureLogging: {},
  loader: {
    target: 'web'
  },
  mode: 'none',
  module: {
    noParse: undefined,
    unsafeCache: false,
    parser: {
      javascript: {
        unknownContextRequest: '.',
        unknownContextRegExp: false,
        unknownContextRecursive: true,
        unknownContextCritical: true,
        exprContextRequest: '.',
        exprContextRegExp: false,
        exprContextRecursive: true,
        exprContextCritical: true,
        wrappedContextRegExp: /.*/,
        wrappedContextRecursive: true,
        wrappedContextCritical: false,
        strictExportPresence: false,
        strictThisContextOnImports: false
      },
      asset: {
        dataUrlCondition: {
          maxSize: 8096
        }
      }
    },
    generator: {},
    defaultRules: [{
      mimetype: 'application/node',
      type: 'javascript/auto'
    }, {
      test: /.json$/i,
      type: 'json'
    }, {
      mimetype: 'application/json',
      type: 'json'
    }, {
      test: /.mjs$/i,
      type: 'javascript/esm',
      resolve: {
        byDependency: {
          esm: {
            fullySpecified: true
          }
        }
      }
    }, {
      test: /.js$/i,
      descriptionData: {
        type: 'module'
      },
      type: 'javascript/esm',
      resolve: {
        byDependency: {
          esm: {
            fullySpecified: true
          }
        }
      }
    }, {
      test: /.cjs$/i,
      type: 'javascript/dynamic'
    }, {
      test: /.js$/i,
      descriptionData: {
        type: 'commonjs'
      },
      type: 'javascript/dynamic'
    }, {
      mimetype: {
        or: ['text/javascript', 'application/javascript']
      },
      type: 'javascript/esm',
      resolve: {
        byDependency: {
          esm: {
            fullySpecified: true
          }
        }
      }
    }, {
      dependency: 'url',
      type: 'asset/resource'
    }],
    rules: []
  },
  name: undefined,
  node: {
    global: true,
    __filename: 'mock',
    __dirname: 'mock'
  },
  optimization: {
    runtimeChunk: false,
    splitChunks: {
      defaultSizeTypes: ['javascript', 'unknown'],
      cacheGroups: {
        'default': {
          idHint: '',
          reuseExistingChunk: true,
          minChunks: 2,
          priority: -20
        },
        defaultVendors: {
          idHint: 'vendors',
          reuseExistingChunk: true,
          test: /[\/]node_modules[\/]/i,
          priority: -10
        }
      },
      hidePathInfo: false,
      chunks: 'async',
      usedExports: false,
      minChunks: 1,
      minSize: 10000,
      minRemainingSize: undefined,
      enforceSizeThreshold: 30000,
      maxAsyncRequests: Infinity,
      maxInitialRequests: Infinity,
      automaticNameDelimiter: '-'
    },
    emitOnErrors: true,
    removeAvailableModules: false,
    removeEmptyChunks: true,
    mergeDuplicateChunks: true,
    flagIncludedChunks: false,
    moduleIds: 'natural',
    chunkIds: 'natural',
    sideEffects: 'flag',
    providedExports: true,
    usedExports: false,
    innerGraph: false,
    mangleExports: false,
    concatenateModules: false,
    checkWasmTypes: false,
    mangleWasmImports: false,
    portableRecords: false,
    realContentHash: false,
    minimize: false,
    minimizer: [{
      apply: compiler => {
        // Lazy load the Terser plugin
        const TerserPlugin = require("terser-webpack-plugin");
        new TerserPlugin({
          terserOptions: {
            compress: {
              passes: 2
            }
          }
        }).apply(compiler);
      }
    }],
    nodeEnv: false
  },
  output: {
    assetModuleFilename: '[hash][ext][query]',
    charset: true,
    chunkFilename: '[name].js',
    chunkFormat: 'array-push',
    chunkLoading: 'jsonp',
    chunkLoadingGlobal: 'webpackChunktest_npm',
    chunkLoadTimeout: 120000,
    clean: undefined,
    compareBeforeEmit: true,
    crossOriginLoading: false,
    devtoolFallbackModuleFilenameTemplate: undefined,
    devtoolModuleFilenameTemplate: undefined,
    devtoolNamespace: 'test-npm',
    environment: {
      arrowFunction: true,
      'const': true,
      destructuring: true,
      forOf: true,
      bigIntLiteral: undefined,
      dynamicImport: undefined,
      module: undefined
    },
    enabledChunkLoadingTypes: ['jsonp', 'import-scripts'],
    enabledLibraryTypes: [],
    enabledWasmLoadingTypes: ['fetch'],
    filename: '[name].js',
    globalObject: 'self',
    hashDigest: 'hex',
    hashDigestLength: 20,
    hashFunction: 'md4',
    hashSalt: undefined,
    hotUpdateChunkFilename: '[id].[fullhash].hot-update.js',
    hotUpdateGlobal: 'webpackHotUpdatetest_npm',
    hotUpdateMainFilename: '[runtime].[fullhash].hot-update.json',
    iife: true,
    importFunctionName: 'import',
    importMetaName: 'import.meta',
    scriptType: false,
    library: undefined,
    module: false,
    path: '/Users/test/dist',
    pathinfo: false,
    publicPath: 'auto',
    sourceMapFilename: '[file].map[query]',
    sourcePrefix: undefined,
    strictModuleExceptionHandling: false,
    uniqueName: 'test-npm',
    wasmLoading: 'fetch',
    webassemblyModuleFilename: '[hash].module.wasm',
    workerChunkLoading: 'import-scripts',
    workerWasmLoading: 'fetch'
  },
  parallelism: 100,
  performance: false,
  plugins: [],
  profile: false,
  recordsInputPath: false,
  recordsOutputPath: false,
  resolve: {
    byDependency: {
      wasm: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      esm: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      loaderImport: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      worker: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...'],
        preferRelative: true
      },
      commonjs: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      amd: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      loader: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      unknown: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      undefined: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      url: {
        preferRelative: true
      }
    },
    cache: false,
    modules: ['node_modules'],
    conditionNames: ['webpack', 'production', 'browser'],
    mainFiles: ['index'],
    extensions: [],
    aliasFields: [],
    exportsFields: ['exports'],
    roots: ['/Users/test'],
    mainFields: ['main']
  },
  resolveLoader: {
    cache: false,
    conditionNames: ['loader', 'require', 'node'],
    exportsFields: ['exports'],
    mainFields: ['loader', 'main'],
    extensions: ['.js'],
    mainFiles: ['index']
  },
  snapshot: {
    resolveBuildDependencies: {
      timestamp: true,
      hash: true
    },
    buildDependencies: {
      timestamp: true,
      hash: true
    },
    resolve: {
      timestamp: true
    },
    module: {
      timestamp: true
    },
    immutablePaths: [],
    managedPaths: ['/Users/test/node_modules']
  },
  stats: {},
  target: 'web',
  watch: false,
  watchOptions: {}
}
复制代码
```

### `mode: 'development'`

```
module.exports = {
  amd: undefined,
  bail: undefined,
  cache: {
    type: 'memory',
    maxGenerations: Infinity
  },
  context: '/Users/test',
  dependencies: undefined,
  devServer: undefined,
  devtool: 'eval',
  entry: {
    main: {
      'import': ['./src']
    }
  },
  experiments: {
    topLevelAwait: false,
    syncWebAssembly: false,
    asyncWebAssembly: false,
    outputModule: false
  },
  externals: undefined,
  externalsPresets: {
    web: true,
    node: false,
    nwjs: false,
    electron: false,
    electronMain: false,
    electronPreload: false,
    electronRenderer: false
  },
  externalsType: 'var',
  ignoreWarnings: undefined,
  infrastructureLogging: {},
  loader: {
    target: 'web'
  },
  mode: 'development',
  module: {
    noParse: undefined,
    unsafeCache: module => {
      const name = module.nameForCondition();
      return name && NODE_MODULES_REGEXP.test(name);
    },
    parser: {
      javascript: {
        unknownContextRequest: '.',
        unknownContextRegExp: false,
        unknownContextRecursive: true,
        unknownContextCritical: true,
        exprContextRequest: '.',
        exprContextRegExp: false,
        exprContextRecursive: true,
        exprContextCritical: true,
        wrappedContextRegExp: /.*/,
        wrappedContextRecursive: true,
        wrappedContextCritical: false,
        strictExportPresence: false,
        strictThisContextOnImports: false
      },
      asset: {
        dataUrlCondition: {
          maxSize: 8096
        }
      }
    },
    generator: {},
    defaultRules: [{
      mimetype: 'application/node',
      type: 'javascript/auto'
    }, {
      test: /.json$/i,
      type: 'json'
    }, {
      mimetype: 'application/json',
      type: 'json'
    }, {
      test: /.mjs$/i,
      type: 'javascript/esm',
      resolve: {
        byDependency: {
          esm: {
            fullySpecified: true
          }
        }
      }
    }, {
      test: /.js$/i,
      descriptionData: {
        type: 'module'
      },
      type: 'javascript/esm',
      resolve: {
        byDependency: {
          esm: {
            fullySpecified: true
          }
        }
      }
    }, {
      test: /.cjs$/i,
      type: 'javascript/dynamic'
    }, {
      test: /.js$/i,
      descriptionData: {
        type: 'commonjs'
      },
      type: 'javascript/dynamic'
    }, {
      mimetype: {
        or: ['text/javascript', 'application/javascript']
      },
      type: 'javascript/esm',
      resolve: {
        byDependency: {
          esm: {
            fullySpecified: true
          }
        }
      }
    }, {
      dependency: 'url',
      type: 'asset/resource'
    }],
    rules: []
  },
  name: undefined,
  node: {
    global: true,
    __filename: 'mock',
    __dirname: 'mock'
  },
  optimization: {
    runtimeChunk: false,
    splitChunks: {
      defaultSizeTypes: ['javascript', 'unknown'],
      cacheGroups: {
        'default': {
          idHint: '',
          reuseExistingChunk: true,
          minChunks: 2,
          priority: -20
        },
        defaultVendors: {
          idHint: 'vendors',
          reuseExistingChunk: true,
          test: /[\/]node_modules[\/]/i,
          priority: -10
        }
      },
      hidePathInfo: false,
      chunks: 'async',
      usedExports: false,
      minChunks: 1,
      minSize: 10000,
      minRemainingSize: 0,
      enforceSizeThreshold: 30000,
      maxAsyncRequests: Infinity,
      maxInitialRequests: Infinity,
      automaticNameDelimiter: '-'
    },
    emitOnErrors: true,
    removeAvailableModules: false,
    removeEmptyChunks: true,
    mergeDuplicateChunks: true,
    flagIncludedChunks: false,
    moduleIds: 'named',
    chunkIds: 'named',
    sideEffects: 'flag',
    providedExports: true,
    usedExports: false,
    innerGraph: false,
    mangleExports: false,
    concatenateModules: false,
    checkWasmTypes: false,
    mangleWasmImports: false,
    portableRecords: false,
    realContentHash: false,
    minimize: false,
    minimizer: [{
      apply: compiler => {
        // Lazy load the Terser plugin
        const TerserPlugin = require("terser-webpack-plugin");
        new TerserPlugin({
          terserOptions: {
            compress: {
              passes: 2
            }
          }
        }).apply(compiler);
      }
    }],
    nodeEnv: 'development'
  },
  output: {
    assetModuleFilename: '[hash][ext][query]',
    charset: true,
    chunkFilename: '[name].js',
    chunkFormat: 'array-push',
    chunkLoading: 'jsonp',
    chunkLoadingGlobal: 'webpackChunktest_npm',
    chunkLoadTimeout: 120000,
    clean: undefined,
    compareBeforeEmit: true,
    crossOriginLoading: false,
    devtoolFallbackModuleFilenameTemplate: undefined,
    devtoolModuleFilenameTemplate: undefined,
    devtoolNamespace: 'test-npm',
    environment: {
      arrowFunction: true,
      'const': true,
      destructuring: true,
      forOf: true,
      bigIntLiteral: undefined,
      dynamicImport: undefined,
      module: undefined
    },
    enabledChunkLoadingTypes: ['jsonp', 'import-scripts'],
    enabledLibraryTypes: [],
    enabledWasmLoadingTypes: ['fetch'],
    filename: '[name].js',
    globalObject: 'self',
    hashDigest: 'hex',
    hashDigestLength: 20,
    hashFunction: 'md4',
    hashSalt: undefined,
    hotUpdateChunkFilename: '[id].[fullhash].hot-update.js',
    hotUpdateGlobal: 'webpackHotUpdatetest_npm',
    hotUpdateMainFilename: '[runtime].[fullhash].hot-update.json',
    iife: true,
    importFunctionName: 'import',
    importMetaName: 'import.meta',
    scriptType: false,
    library: undefined,
    module: false,
    path: '/Users/test/dist',
    pathinfo: true,
    publicPath: 'auto',
    sourceMapFilename: '[file].map[query]',
    sourcePrefix: undefined,
    strictModuleExceptionHandling: false,
    uniqueName: 'test-npm',
    wasmLoading: 'fetch',
    webassemblyModuleFilename: '[hash].module.wasm',
    workerChunkLoading: 'import-scripts',
    workerWasmLoading: 'fetch'
  },
  parallelism: 100,
  performance: false,
  plugins: [],
  profile: false,
  recordsInputPath: false,
  recordsOutputPath: false,
  resolve: {
    byDependency: {
      wasm: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      esm: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      loaderImport: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      worker: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...'],
        preferRelative: true
      },
      commonjs: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      amd: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      loader: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      unknown: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      undefined: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      url: {
        preferRelative: true
      }
    },
    cache: true,
    modules: ['node_modules'],
    conditionNames: ['webpack', 'development', 'browser'],
    mainFiles: ['index'],
    extensions: [],
    aliasFields: [],
    exportsFields: ['exports'],
    roots: ['/Users/test'],
    mainFields: ['main']
  },
  resolveLoader: {
    cache: true,
    conditionNames: ['loader', 'require', 'node'],
    exportsFields: ['exports'],
    mainFields: ['loader', 'main'],
    extensions: ['.js'],
    mainFiles: ['index']
  },
  snapshot: {
    resolveBuildDependencies: {
      timestamp: true,
      hash: true
    },
    buildDependencies: {
      timestamp: true,
      hash: true
    },
    resolve: {
      timestamp: true
    },
    module: {
      timestamp: true
    },
    immutablePaths: [],
    managedPaths: ['/Users/test/node_modules']
  },
  stats: {},
  target: 'web',
  watch: false,
  watchOptions: {}
}
复制代码
```

### `mode: 'production'`

```
module.exports = {
  amd: undefined,
  bail: undefined,
  cache: false,
  context: '/Users/test',
  dependencies: undefined,
  devServer: undefined,
  devtool: false,
  entry: {
    main: {
      'import': ['./src']
    }
  },
  experiments: {
    topLevelAwait: false,
    syncWebAssembly: false,
    asyncWebAssembly: false,
    outputModule: false
  },
  externals: undefined,
  externalsPresets: {
    web: true,
    node: false,
    nwjs: false,
    electron: false,
    electronMain: false,
    electronPreload: false,
    electronRenderer: false
  },
  externalsType: 'var',
  ignoreWarnings: undefined,
  infrastructureLogging: {},
  loader: {
    target: 'web'
  },
  mode: 'production',
  module: {
    noParse: undefined,
    unsafeCache: false,
    parser: {
      javascript: {
        unknownContextRequest: '.',
        unknownContextRegExp: false,
        unknownContextRecursive: true,
        unknownContextCritical: true,
        exprContextRequest: '.',
        exprContextRegExp: false,
        exprContextRecursive: true,
        exprContextCritical: true,
        wrappedContextRegExp: /.*/,
        wrappedContextRecursive: true,
        wrappedContextCritical: false,
        strictExportPresence: false,
        strictThisContextOnImports: false
      },
      asset: {
        dataUrlCondition: {
          maxSize: 8096
        }
      }
    },
    generator: {},
    defaultRules: [{
      mimetype: 'application/node',
      type: 'javascript/auto'
    }, {
      test: /.json$/i,
      type: 'json'
    }, {
      mimetype: 'application/json',
      type: 'json'
    }, {
      test: /.mjs$/i,
      type: 'javascript/esm',
      resolve: {
        byDependency: {
          esm: {
            fullySpecified: true
          }
        }
      }
    }, {
      test: /.js$/i,
      descriptionData: {
        type: 'module'
      },
      type: 'javascript/esm',
      resolve: {
        byDependency: {
          esm: {
            fullySpecified: true
          }
        }
      }
    }, {
      test: /.cjs$/i,
      type: 'javascript/dynamic'
    }, {
      test: /.js$/i,
      descriptionData: {
        type: 'commonjs'
      },
      type: 'javascript/dynamic'
    }, {
      mimetype: {
        or: ['text/javascript', 'application/javascript']
      },
      type: 'javascript/esm',
      resolve: {
        byDependency: {
          esm: {
            fullySpecified: true
          }
        }
      }
    }, {
      dependency: 'url',
      type: 'asset/resource'
    }],
    rules: []
  },
  name: undefined,
  node: {
    global: true,
    __filename: 'mock',
    __dirname: 'mock'
  },
  optimization: {
    runtimeChunk: false,
    splitChunks: {
      defaultSizeTypes: ['javascript', 'unknown'],
      cacheGroups: {
        'default': {
          idHint: '',
          reuseExistingChunk: true,
          minChunks: 2,
          priority: -20
        },
        defaultVendors: {
          idHint: 'vendors',
          reuseExistingChunk: true,
          test: /[\/]node_modules[\/]/i,
          priority: -10
        }
      },
      hidePathInfo: true,
      chunks: 'async',
      usedExports: true,
      minChunks: 1,
      minSize: 20000,
      minRemainingSize: undefined,
      enforceSizeThreshold: 50000,
      maxAsyncRequests: 30,
      maxInitialRequests: 30,
      automaticNameDelimiter: '-'
    },
    emitOnErrors: false,
    removeAvailableModules: false,
    removeEmptyChunks: true,
    mergeDuplicateChunks: true,
    flagIncludedChunks: true,
    moduleIds: 'deterministic',
    chunkIds: 'deterministic',
    sideEffects: true,
    providedExports: true,
    usedExports: true,
    innerGraph: true,
    mangleExports: true,
    concatenateModules: true,
    checkWasmTypes: true,
    mangleWasmImports: false,
    portableRecords: false,
    realContentHash: true,
    minimize: true,
    minimizer: [{
      apply: compiler => {
        // Lazy load the Terser plugin
        const TerserPlugin = require("terser-webpack-plugin");
        new TerserPlugin({
          terserOptions: {
            compress: {
              passes: 2
            }
          }
        }).apply(compiler);
      }
    }],
    nodeEnv: 'production'
  },
  output: {
    assetModuleFilename: '[hash][ext][query]',
    charset: true,
    chunkFilename: '[name].js',
    chunkFormat: 'array-push',
    chunkLoading: 'jsonp',
    chunkLoadingGlobal: 'webpackChunktest_npm',
    chunkLoadTimeout: 120000,
    clean: undefined,
    compareBeforeEmit: true,
    crossOriginLoading: false,
    devtoolFallbackModuleFilenameTemplate: undefined,
    devtoolModuleFilenameTemplate: undefined,
    devtoolNamespace: 'test-npm',
    environment: {
      arrowFunction: true,
      'const': true,
      destructuring: true,
      forOf: true,
      bigIntLiteral: undefined,
      dynamicImport: undefined,
      module: undefined
    },
    enabledChunkLoadingTypes: ['jsonp', 'import-scripts'],
    enabledLibraryTypes: [],
    enabledWasmLoadingTypes: ['fetch'],
    filename: '[name].js',
    globalObject: 'self',
    hashDigest: 'hex',
    hashDigestLength: 20,
    hashFunction: 'md4',
    hashSalt: undefined,
    hotUpdateChunkFilename: '[id].[fullhash].hot-update.js',
    hotUpdateGlobal: 'webpackHotUpdatetest_npm',
    hotUpdateMainFilename: '[runtime].[fullhash].hot-update.json',
    iife: true,
    importFunctionName: 'import',
    importMetaName: 'import.meta',
    scriptType: false,
    library: undefined,
    module: false,
    path: '/Users/test/dist',
    pathinfo: false,
    publicPath: 'auto',
    sourceMapFilename: '[file].map[query]',
    sourcePrefix: undefined,
    strictModuleExceptionHandling: false,
    uniqueName: 'test-npm',
    wasmLoading: 'fetch',
    webassemblyModuleFilename: '[hash].module.wasm',
    workerChunkLoading: 'import-scripts',
    workerWasmLoading: 'fetch'
  },
  parallelism: 100,
  performance: {
    maxAssetSize: 250000,
    maxEntrypointSize: 250000,
    hints: 'warning'
  },
  plugins: [],
  profile: false,
  recordsInputPath: false,
  recordsOutputPath: false,
  resolve: {
    byDependency: {
      wasm: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      esm: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      loaderImport: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      worker: {
        conditionNames: ['import', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...'],
        preferRelative: true
      },
      commonjs: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      amd: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      loader: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      unknown: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      undefined: {
        conditionNames: ['require', 'module', '...'],
        extensions: ['.js', '.json', '.wasm'],
        aliasFields: ['browser'],
        mainFields: ['browser', 'module', '...']
      },
      url: {
        preferRelative: true
      }
    },
    cache: false,
    modules: ['node_modules'],
    conditionNames: ['webpack', 'production', 'browser'],
    mainFiles: ['index'],
    extensions: [],
    aliasFields: [],
    exportsFields: ['exports'],
    roots: ['/Users/test'],
    mainFields: ['main']
  },
  resolveLoader: {
    cache: false,
    conditionNames: ['loader', 'require', 'node'],
    exportsFields: ['exports'],
    mainFields: ['loader', 'main'],
    extensions: ['.js'],
    mainFiles: ['index']
  },
  snapshot: {
    resolveBuildDependencies: {
      timestamp: true,
      hash: true
    },
    buildDependencies: {
      timestamp: true,
      hash: true
    },
    resolve: {
      timestamp: true,
      hash: true
    },
    module: {
      timestamp: true,
      hash: true
    },
    immutablePaths: [],
    managedPaths: ['/Users/test/node_modules']
  },
  stats: {},
  target: 'web',
  watch: false,
  watchOptions: {}
}
复制代码
```

## 一点感想

webpack的配置十分复杂，对构建过程涉及到的细节做成了可配置的，给人的感觉是增加了很大的学习成本，很多配置的来龙去脉，读文档也未必能够理解其中的作用，去研究源码会投入过多的精力，往往投入产出比很低。

在一般的开发场景中，我们并不需要如此细致地对webpack的配置进行学习，webpack通过模式已经提供了最佳实践配置，而我们要做的，只不过是指定mode，然后视情况配置entry、output、loader、plugin而已。只有当默认的配置无法满足实际情况时，才需要对它进一步理解，这种情况少之又少，我们的重心应该放在更具有意义的业务价值上吧。
