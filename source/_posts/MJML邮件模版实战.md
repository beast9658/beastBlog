---
title: MJML邮件模版实战
date: 2022-09-07 22:13:14
---
![MJML邮件模版实战](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6cccc1ec36bf4e82a22a74ff7d6d9f85~tplv-k3u1fbpfcp-zoom-1.image)

### 前言

最近画个邮件模版使用table布局一言难尽，还要兼容各种邮件厂商，最后找了一个邮件模版来处理。

### MJML

MJML是一种标记语言，旨在减少编写响应电子邮件的痛苦。它的语义语法使它简单明了，它丰富的标准组件库加快了您的开发时间，并减轻了您的电子邮件代码库。MJML的开源引擎生成符合最佳实践的高质量响应HTML。

### 环境搭建

创建个文件夹`demo` -> 进入`demo`目录

初始化项目

```
npm init -y
复制代码
```

安装mjml包

```
npm install mjml
复制代码
```

创建`demo.mjml`文件

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3a82543baef74d83909c357cd5f1efc8~tplv-k3u1fbpfcp-zoom-1.image)

### 页面编写

vscode 和 webstorm都有预览插件，直接搜索MJML即可。 建议还是在官网在线编写，然后在复制到代码保存。

在线编写地址: [mjml.io/try-it-live](https://link.juejin.cn/?target=https%3A%2F%2Fmjml.io%2Ftry-it-live "https://mjml.io/try-it-live")

当然你也可以用可视化界面来编辑：[grapesjs.com/demo-mjml.h…](https://link.juejin.cn/?target=https%3A%2F%2Fgrapesjs.com%2Fdemo-mjml.html "https://grapesjs.com/demo-mjml.html")

这是一封回复邮件

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b8612992840f443cb58645835b81387b~tplv-k3u1fbpfcp-zoom-1.image)

像普通的HTML模板一样，我们可以将这个模板分成不同的部分以适应网格。 电子邮件正文，由`mj-body`标记包含文档的全部内容：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/10bb16773d0f48308af1b2701d527252~tplv-k3u1fbpfcp-zoom-1.image)

```
<mjml>
  <mj-body>
    to do..
  </mj-body>
</mjml>
复制代码
```

从这里，您可以首先定义您的分区：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/11a8f18c2322433bb0ab1a9b064aa46b~tplv-k3u1fbpfcp-zoom-1.image)

```
<mjml>
  <mj-body>
    <mj-section>
      1
    </mj-section>
    <mj-section>
      2
    </mj-section>
    <mj-section>
      3
    </mj-section>
    <mj-section>
      4
    </mj-section>
  </mj-body>
</mjml>
复制代码
```

在任何部分中，都应该有列（即使您只需要一列），列是使MJML响应的原因。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/360589202c5f42329369958d37b7e3dc~tplv-k3u1fbpfcp-zoom-1.image)

```
<mjml>
  <mj-body>
     <!-- 1 -->
     <mj-section background-url="http://1.bp.blogspot.com/-TPrfhxbYpDY/Uh3Refzk02I/AAAAAAAALw8/5sUJ0UUGYuw/s1600/New+York+in+The+1960's+-+70's+(2).jpg" background-size="cover" background-repeat="no-repeat">
      <mj-column width="600px">
        <mj-text align="center" color="#fff" font-size="40px" font-family="Helvetica Neue">Subject</mj-text>
      </mj-column>
    </mj-section>
    <!-- 2 -->
    <mj-section>
      <mj-column >
        <mj-text color="#000" font-size="16px" font-family="Helvetica Neue">Lorem ipsum dolor sit amet,consectetur adipiscing elit.sit amet,consectetur adipiscing elit.sit amet,consectetur adipiscing elit.sit amet,consectetur adipiscing elit.sit amet,consectetur adipiscing elit.</mj-text>
      </mj-column>
    </mj-section>
    <!-- 3 -->
    <mj-section>
      <mj-column >
       <mj-divider padding-left="0px" padding-right="0px"></mj-divider>
      </mj-column>
    </mj-section>
    <!-- 4 -->
    <mj-section background-color="#ffffff"  full-width="full-width">
      <mj-column vertical-align="top" width="33.33333333333333%">
        <mj-image src="http://191n.mj.am/img/191n/1t/hs.png" alt="" width="50px"></mj-image>
        <mj-text align="center" color="#9da3a3" font-size="11px" padding-bottom="30px"><span style="font-size: 14px; color: #e85034">Best audience</span><br /><br />Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque eleifend sagittis nunc, et fermentum est ullamcorper dignissim.</mj-text>
      </mj-column>
      <mj-column vertical-align="top" width="33.33333333333333%">
        <mj-image src="http://191n.mj.am/img/191n/1t/hm.png" alt="" width="50px"></mj-image>
        <mj-text align="center" color="#9da3a3" font-size="11px" padding-bottom="30px"><span style="font-size: 14px; color: #e85034">Higher rates</span><br /><br />Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque eleifend sagittis nunc, et fermentum est ullamcorper dignissim.</mj-text>
      </mj-column>
      <mj-column vertical-align="top" width="33.33333333333333%">
        <mj-image src="http://191n.mj.am/img/191n/1t/hl.png" alt="" width="50px"></mj-image>
        <mj-text align="center" color="#9da3a3" font-size="11px" padding-bottom="30px" padding-top="3px"><span style="font-size: 14px; color: #e85034">24/7 Support</span><br /><br />Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque eleifend sagittis nunc, et fermentum est ullamcorper dignissim.</mj-text>
      </mj-column>
    </mj-section>
  </mj-body>
</mjml>
复制代码
```

至此我们就得到一个响应式布局的模板。

### 编译

```
mjml demo.mjml -o demo.html
复制代码
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2177848a62f64ceba727eb97e4155c2a~tplv-k3u1fbpfcp-zoom-1.image)

编译完成即可看到我们生成的html，打开浏览器进行预览。

### 发送测试邮件

这里采用 `nodemailer`包，来发送邮件。 官网地址: [nodemailer](https://link.juejin.cn/?target=https%3A%2F%2Fnodemailer.com%2F "https://nodemailer.com/").

```
npm install nodemailer fs path
复制代码
```

```
// send.js

var nodemailer = require('nodemailer');
var fs = require('fs')
var path = require('path')
var transporter = nodemailer.createTransport({
    service: 'qq',
    auth: {
        user: '11111111@qq.com', // 你的账号
        pass: 'xxxxxxxx' //你的qq授权码
    }
});
var mailOptions = {
    from: '"nick" <11111111@qq.com>', // 你的账号名 | 你的账号
    to: '222222@qq.com,333333@qq.com,', // 接受者,可以同时发送多个,以逗号隔开
    subject: 'MJML', // 标题
    html: fs.createReadStream(path.resolve(__dirname,'demo.html')) // 指定发送文件路径
};

transporter.sendMail(mailOptions, function (err, info) {
    if (err) {
        console.log(err);
        return;
    }
    console.log('发送成功');
});

复制代码
```

qq授权码获取:打开qq邮箱，找到设置，打开配置，你就能得到qq授权码。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/305258bcf0834ea79e3d5259ca332fd1~tplv-k3u1fbpfcp-zoom-1.image)

运行命令

```
node send.js
复制代码
```

至此邮件已经发送到你的邮箱。

### 自动编译

后续邮件越写越多，文件目标很多，不太想一个个手动编译成html,所以写个脚本自动编译输出html文件。

新增 src目录 新增 build.js

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/baa55d9dc7e34d328b4b4b5560a1e908~tplv-k3u1fbpfcp-zoom-1.image)

```
// build.js

const fs = require('fs')
const path = require('path');
const child_process = require('child_process');

/**
 * src 目录下文件
 * dist 打包的目标文件
 */
copyDir('./src/*', './dist')


// 复制文件 -> 编译文件
function copyDir(src, dist) {
    if (fs.existsSync(dist)) {
        copyFile(src, dist)
    }else{
        fs.mkdir(dist, function (err) {
            if (err) {
                console.log(err)
                return
            }
            copyFile(src, dist)
        })
    }
    function copyFile(src, dist){
        // 如果是windows
        if(process.platform === 'win32'){
            child_process.exec(`ROBOCOPY src ${dist} /E /MT:30`,function () {
                compile()
            });
        }else{
            child_process.exec(`cp -r ${src} ${dist}`,function () {
                compile()
            });
        }
    }
}
// 编译文件
function compile( src = 'dist') {
    let startPath = process.cwd()
    fs.readdir(src,{withFileTypes:true},  function (err, files) {
        files.forEach(file=>{
            if(file.isDirectory()){
                let dir = `${src}/${file.name}`
                compile(dir)
            }else{
                // 获取当前文件名称
                let fileName = file.name.split('.')[0]
                // 具体目录
                process.chdir(src);
                // 如果是windows
                if(process.platform === 'win32'){
                    // 编译MJML文件
                    const compile = child_process.exec(`mjml ${fileName}.mjml -o ${fileName}.html`)
                    compile.on('close', (code) => {
                        console.log('编译成功:',`${fileName}.mjml`)
                        let filePath = path.join(__dirname,`${src}/${fileName}.mjml`)
                        child_process.exec(`del ${filePath}`)
                    });
                }else{
                    // 编译MJML文件
                    const compile = child_process.spawn(`mjml`, [`${fileName}.mjml`,`-o`,`${fileName}.html`], {cwd: path.resolve(__dirname, src)})
                    compile.on('close', code => {
                        // 删除MJML文件
                        child_process.spawn(`rm`, [`${fileName}.mjml`], {cwd: path.resolve(__dirname, src)})
                        console.log('编译成功:',`${fileName}.mjml`)
                    })
                }
                // 复原目录
                process.chdir(startPath);
            }
        })
    })
}

复制代码
```

运行命令

```
node build.js
复制代码
```

会自动编译生成和src目录相同的html结构。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/24c8a12fa17e4412ae6667090a4fc843~tplv-k3u1fbpfcp-zoom-1.image)

### 结语

MJML在几个邮件厂商测试样式偏差不大，值得使用。 如果文章对你有帮助，请帮我点赞收藏，谢谢大家。

### 参考

HTML Email 编写指南【阮一峰】：[地址](https://link.juejin.cn/?target=http%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2013%2F06%2Fhtml_email.html "http://www.ruanyifeng.com/blog/2013/06/html_email.html")

github demo地址:[github](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fanick-xs%2FMJML-Node "https://github.com/anick-xs/MJML-Node")