---
title: 一些VSCode上经常使用的代码格式配置
date: 2023-02-25 16:30:49
---
### 使用到的插件

`Prettier` ， `vetur`

具体JSON

```
{
    "workbench.iconTheme": "material-icon-theme",
    "vsicons.dontShowNewVersionMessage": true,
    "editor.suggestSelection": "first",
    //配置未使用变量变灰
    "editor.showUnused": true,
    // 重新设定tabsize
    "editor.tabSize": 4,
    //  #让函数(名)和后面的括号之间加个空格
    "javascript.format.insertSpaceBeforeFunctionParenthesis": true,
    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
    "files.exclude": {
        "**/.classpath": true,
        "**/.project": true,
        "**/.settings": true,
        "**/.factorypath": true
    },
    "explorer.confirmDelete": false,
    "files.associations": {
        "*.cjson": "jsonc",
        "*.wxss": "css",
        "*.wxs": "javascript"
    },
    "emmet.includeLanguages": {
        "wxml": "html"
    },
    "minapp-vscode.disableAutoConfig": true,
    "workbench.colorTheme": "Nebula",
    "window.zoomLevel": -1,
    "terminal.integrated.rendererType": "dom",
    "python.jediEnabled": false,
    "update.mode": "none",
    "update.enableWindowsBackgroundUpdates": false,
    // "[javascript]": {
    //     "editor.defaultFormatter": "HookyQR.beautify"
    // },
    "[vue]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.showUnused": true,
    },
    "vetur.validation.template": false,
    "todo-tree.tree.showScanModeButton": false,
    // 格式化插件设置为 prettier,以下是prettier的配置
    "[html]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[javascript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[typescript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[javascriptreact]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[typescriptreact]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[less]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[css]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[json]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[jsonc]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "prettier.tabWidth": 4, // 缩进字节数
    "prettier.printWidth": 100, // 
    "prettier.trailingComma": "es5", // 在对象或数组最后一个元素后面是否加逗号（在ES5中加尾逗号）
    "prettier.useTabs": false, // 缩进不使用tab，使用空格
    "prettier.semi": true, // 句尾添加分号
    "prettier.singleQuote": true, // 不使用单引号代替双引号
    "prettier.proseWrap": "preserve", // 默认值。因为使用了一些折行敏感型的渲染器（如GitHub comment）而按照markdown文本样式进行折行
    // 箭头函数参数括号 默认avoid 可选 avoid| always
    // avoid 能省略括号的时候就省略 例如x => x
    // always 总是有括号
    // (x) => {} 箭头函数参数只有一个时是否要有小括号。avoid：省略括号
    "prettier.arrowParens": "avoid", 
    "prettier.bracketSpacing": true, // 在对象，数组括号与文字之间加空格 "{ foo: bar }"
    // "prettier.disableLanguages": ["vue"], // 不格式化vue文件，vue文件的格式化单独设置
    "prettier.endOfLine": "auto", //不让prettier使用eslint的代码格式进行校验
    "prettier.htmlWhitespaceSensitivity": "ignore",
    "prettier.ignorePath": ".prettierignore", // 不使用prettier格式化的文件填写在项目的.prettierignore文件中
    "prettier.jsxBracketSameLine": true, // 在jsx中把'>' 单独放一行
    "prettier.jsxSingleQuote": false, // 在jsx中使用单引号代替双引号
    // "prettier.parser": "babylon", // 格式化的解析器，默认是babylon
    "prettier.requireConfig": false, // 不让prettier使用tslint的代码格式进行校验
}
```