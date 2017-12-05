# vue-webpack-pages

This is a template of webpack3.0 template

基于vue-cli 的webpack3.0 多页面模版项目

**官方脚手架初始化的SPA应用，如何加以改造，是的可以进行多页面的访问呢？下面就开始改造吧**

1. 找到目录 build/webpack.base.conf.js 进行入口文件的配置修改

   ```
   entry: utils.getEntries('./src/views/**/*.js')
   ```

2. utils 添加读取页面的对象方法

   ```
   // 读取页面入口
   exports.getEntries = function (globPath) {
     var entries = {}, basename, tmp, pathname;

     glob.sync(globPath).forEach(function (entry) {
       basename = path.basename(entry, path.extname(entry));
       tmp = entry.split('/').splice(-3);
       pathname = tmp.splice(0, 1) + '/' + basename; // 正确输出js和html的路径
       entries[pathname] = entry;
     });
     console.log(entries);
     return entries;
   }
   ```

3. 修改 web pack.dev.conf.js 文件 

   1. 删除plugins中关于htmlPlugin的配置信息
   2. 添加与入口文件对应入口文件读取

   ```
   // 读取所有页面入口
   var pages = utils.getEntries('./src/views/**/*.html');
   // 逐一进行生成
   for (var pathname in pages) {
     // 配置生成的html文件，定义路径等
     var conf = {
       filename: pathname.split('/')[1] + '.html',
       template: pages[pathname],  // 模板路径
       inject: true,               // js插入位置
       chunks: [pathname]          // 仅嵌入当前目录下JS
     };
     // https://github.com/ampedandwired/html-webpack-plugin
     devWebpackConfig.plugins.push(new HtmlWebpackPlugin(conf));
   }
   ```

4. 修改webpack.prod.conf.js 

   ```
   // 读取页面入口
   var pages = utils.getEntries('./src/views/**/*.html');

   // 逐个生成操作
   for (var pathname in pages) {
     console.log(pathname);
     // 配置生成的html文件，定义路径等
     var conf = {
       // filename: pathname + '.html',
       filename: pathname.split('/')[1] + '.html',
       template: pages[pathname], // 模板路径
       inject: true,
       minify: {
         removeComments: true,
         collapseWhitespace: true,
         removeAttributeQuotes: true
         // more options:
         // https://github.com/kangax/html-minifier#options-quick-reference
       },
       chunks: ['vendor', 'manifest', pathname],
       chunksSortMode: 'dependency'
     };
     // 需要生成几个html文件，就配置几个HtmlWebpackPlugin对象
     // generate dist index.html with correct asset hash for caching.
     // you can customize output by editing /index.html
     module.exports.plugins.push(new HtmlWebpackPlugin(conf));
   }
   ```

   ​

#### 修改完成，启动你的项目吧

### 如何启动该项目

```
1.git clone https://github.com/IssacSix/vue-webpack-pages.git
2.npm i (cnpm i)
3.npm run dev
4.浏览器访问 localhost:8090 就能看到页面啦 访问页面1
5.http://localhost:8091/page2.html 访问第二个页面
```

**最后说明一点，这个项目加上了flexibel 支持移动端的单位适配，并且安装了postcss工具进行单位自动换算**

见vue-loader.conf.js 添加postcss对象

```
postcss: [
	require('postcss-px2rem')({ 'remUnit': 75, 'baseDpr': 2 })
]
```

 