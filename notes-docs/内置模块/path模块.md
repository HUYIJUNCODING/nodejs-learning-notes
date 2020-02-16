## nodejs — path 模块

### 前言
path 模块是 nodejs 中用于处理文件/目录路径的一个内置模块，可以看作是一个工具箱，提供诸多方法供我们使用，当然都是和路径处理有关的。同时在前端开发中 path 模块出现的频率也是比较高的，比如配置 webpack 的时候等。本文是对该模块中一些常用的方法进行介绍，走，一起学习下吧。
* 需要注意下，nodejs 中所有的模块（内置，自定义）都需要使用 requier 进行导入，一般导入位置在文件开始位置。
```
const path = require('path');
```

### API
#### basename （获取路径基础名）
* path.basename(path[,ext])
    * path：文件/目录路径
    * ext：（可选）文件扩展名 例如 .js  .css 等
    * 返回值：path 路径的最后一部分
* 注意： 
    * 如果 path 不是字符串或者给定的 ext 参数不是字符串，则抛出 TypeError
    * 如果有 ext 参数，当 ext 后缀名与文件名匹配上时返回的文件名会省略文件后缀
    * 如果 path 尾部有目录分隔符则会被忽略

```js
const path = require("path");

path.basename('./ext/test.js') //test.js
path.basename('./ext/test.js','.js') //test (当后缀名与文件名匹配上时返回的文件名会省略文件后缀)
path.basename('./ext/test.js','.html') //test.js (没有匹配上时返回文件全名)
path.basename('./ext/foo/') // foo (尾部目录分隔符被忽略)
```
#### dirname （获取路径目录名）
* path.dirname(path)
    * path：文件/目录路径
    * 返回值：path 路径的目录名
* 注意： 
    * 如果 path 不是字符串，则抛出 TypeError
    * 如果 path 尾部有目录分隔符则会被忽略

```js
const path = require("path");

path.dirname('./foo/bar/baz'); //./foo/bar (相对路径/绝对路径均可)
path.dirname('/foo/bar/baz/'); // /foo/bar (尾部目录分隔符被忽略)
path.dirname('/foo/bar/baz/test.js'); // /foo/bar/baz
```

#### extname （获取路径扩展名）
* path.extname(path)
    * path：文件/目录路径
    * 返回值：path 路径的扩展名，从最后一次出现 '.' 字符到 path 最后一部分的字符串结束,无扩展名则返回空
* 注意： 
    * 如果 path 不是字符串，则抛出 TypeError

```js
const path = require("path");

path.extname('foo/bar/baz/test.js'); // .js
path.extname('foo/bar/baz');// '' (无扩展名返回 '')
path.extname('foo/bar/baz/.'); // ''
path.extname('foo/bar/baz/test.'); // '.'
path.extname('foo/bar/baz/.test'); // ''
path.extname('foo/bar/baz/.test.js'); // '.js'
```

#### parse （解析路径）
* path.parse(path) ( str => obj )
    * path：文件/目录路径
    * 返回值：带有属性(dir,root,base,name,ext)的对象
         * root：根目录
         * dir： 文件所在的文件夹
         * base：完整文件 ( index.js )
         * name：文件名
         * ext:  文件后缀名
* 注意： 
    * 如果 path 不是字符串，则抛出 TypeError
    * 如果尾部有目录分隔符则会被忽略
* 一图胜千言
```
┌──────────────────┬────────────┐
│          dir     │    base    │
├──────┬           ├──────┬─────┤
│ root │           │ name │ ext │
"  /    foo/bar/baz/ index  .js "
```

```js
const path = require("path");

path.parse('/foo/bar/baz/index.js')
// {
//     root: '/',
//     dir: '/foo/bar/baz',
//     base: 'index.js',
//     ext: '.js',
//     name: 'index'
//   }

path.parse('/foo/bar/baz') //尾部目录分隔符省略
// {
//     root: '/',
//     dir: '/foo/bar',
//     base: 'baz',
//     ext: '',
//     name: 'baz'
//   }

path.parse('./foo/bar/baz/index.js') //当路径为相对路径 ./ 或../时 解析结果中root(代表根目录,绝对路径才有值)为 ''
// {
//     root: '',
//     dir: './foo/bar/baz',
//     base: 'index.js',
//     ext: '.js',
//     name: 'index'
//   }

```

#### format （序列化路径）
*  path.format(pathObj) 序列化 path 路径,与 path.parse() 刚好相反
    * pathObj：path对象
    * 返回值：序列化后的字符串路径 ( obj => string )
    
* 注意： 
    * 如果 pathObject 不是对象，则抛出 TypeError
    * pathObject 中的属性需要注意优先级：
        * 当 dir 属性存在则忽略 root 属性
        * 当 base 属性存在则会忽略 name 和 ext 属性

#### isAbsolute （是否是绝对路径）
* path.isAbsolute(path)
    * path：文件/目录路径
    * 返回值：true/false
* 注意： 
    * 如果 path 不是字符串，则抛出 TypeError
    * 如果给定的 path 字符串长度为0，则返回 false

```js
const path = require("path");

path.isAbsolute('//foo'); // true
path.isAbsolute('\\\\foo'); // true
path.isAbsolute('C:/foo/..'); // true
path.isAbsolute('C:\\foo\\..'); // true
path.isAbsolute('./bar\\baz');  // false
path.isAbsolute('../bar/baz'); // false
path.isAbsolute('.'); // false
path.isAbsolute('');  // false
```

#### join（拼接路径片段）
* path.join([...paths]) 
    * paths：路径片段
    * 返回值：使用平台特定的分隔符作为定界符将所有给定的 path 片段连接在一起规范化后生成的路径
* 注意： 
    * 如果 paths 不是字符串片段，则抛出 TypeError
    * 零长度的 path 片段会被忽略
    * 如果连接后的路径字符长度为0，则返回 '.'，表示当前工作目录
    * 目录分隔符有平台差异,windows 返回为 ' \ '

```js
const path = require("path");

path.join('') // '.'
path.join('./') // '.\'
path.join('../') // '..\'
path.join('/foo/','bar','baz','../','index.js') // '\foo\bar\index.js'
path.join('./bar','baz' ,'/','../','',index.js') // 'bar\index.js'
path.join('foo', {}, 'bar'); // 'TypeError: Path must be a string. Received {}'
```

#### normalize（规范化路径）
* path.normalize(path)
    * path: 文件/目录路径
    * 返回值：规范后的路径字符串
* 注意： 
    * 如果 path 不是字符串片段，则抛出 TypeError
    * 尾部的分隔符会保留
    * 如果 path字符串长度为0，则返回 '.'，表示当前工作目录
    * 路径中的目录分隔符均会被替换成平台特定的目录分隔符,windows 系统 会将 '/' 或'\' 均替换成 '\'
    * 路径中连续的多个分隔符会被规范化为一个
    * 路径中最好不要出现单个 ' \ ',因为当和字母在一起的时候会被当做转义符
```js
const path = require("path");

path.normalize('') // '.'
path.normalize('temp//foo//bar//..//'); // temp\foo\
path.normalize('C:////temp\\\\/\\/\\/foo/bar') // C:\temp\foo\bar
path.normalize('..////foo//\bar/baz/') // ..\fooar\baz\ (转义字符出现)
path.normalize('temp//foo/\bar') // temp\fooar (转义字符出现)

```

#### relative（获取 from 到 to 的相对路径）
* path.relative(from,to)
    * from，to: 文件/目录路径
    * 返回值：from 到 to 的相对路径（to 相对于 form 的相对路径）
* 注意： 
   * 如果 from 和 to 指向相同路径相同 则返回 ''
   * 如果 from 或 to 任何一方为空,则使用当前工作目录代替其空路径

```js
const path = require("path");

//当前工作目录为 \Stone\node\node\path_module
path.relative('/foo/bar/baz','/foo/bar/dir/file.js') // ..\dir\file.js
path.relative('/foo/bar/baz','/foo/bar/baz') // ''
path.relative('/foo/bar/baz/files.js','') // ..\..\..\..\Stone\node\node\path_module
path.relative('','/foo/bar/baz/files.js') // ..\..\..\..\foo\bar\baz\files.js
path.relative('','./foo/bar/baz/files.js') // foo\bar\baz\files.js
```
这里针对 ` from 或 to 任何一方为空,则使用当前工作目录代替其空路径。`稍作说明下，
例如当前工作目录为 `\Stone\node\node\path_module`,则可以看到 `path.relative('/foo/bar/baz/files.js','')` 的输出结果为` ..\..\..\..\Stone\node\node\path_module`，此时 to 为 `\Stone\node\node\path_module`，
要输出 to 相对于 from 的相对路径，则 from 需要先 ../ 的形式 一层一层退出，来检索与 to 的公共父级目录，直到遇到公共父级目录或者到根目录停止，然后cd 进 to 目录。这是针对另一方为绝对路径，如果另一方为相对路径，则直接就是当前另一方路径。

#### resolve（将路径或路径片段的序列解析为绝对路径）
* path.resolve([...paths])
    * paths: 路径或路径片段的序列
    * 返回值：路径或路径片段序列解析为绝对路径。（将路径片段解析后生成的绝对路径）
* 注意： 
   * 路径片段如果给出则必须是字符串类型，否则类型错误
   * 给定的路径序列从右到左进行处理，每个后续的 path 前置，直到构造出一个绝对路径
   * 如果处理完所有给定的 path 片段之后还未生成绝对路径，则再加上当前工作目录
   * 生成的路径均已规范化，并且除非将路径解析为根目录，否则将删除尾部斜杠
   * 零长度的 path 片段会被忽略
   * 若没有传入 path 片段，则 path.resolve() 将返回当前工作目录的绝对路径

```js
const path = require("path");

//当前工作目录为 \Stone\node\node\path_module
path.resolve('/foo/bar', './baz'); // '/foo/bar/baz'
path.resolve('/foo/bar','', '/tmp/file/'); //'/tmp/file'
path.resolve('root', 'foo/baz/', '../fun/bar') // '\Stone\node\node\path_module\root\foo\fun\bar'
path.resolve() // '\Stone\node\node\path_module'

```
path.resolve 方法解析路径片段的时候会从右往左的顺序依次解析，直到构造出一个绝对路径，否则会将当前工作目录加在路径开头。所以，通过 resolve 解析生成的路径一定是绝对路径。这个方法使用的非常多，应该特眼熟，对，前端我们配置 webpack 的时候会高频率用到，并且往往还会结合 `__dirname ` 使用。

#### __dirname，__filename

* __dirname：可以看作是 nodejs 中的全局变量，它始终表示当前执行文件所在目录的完整目录名（绝对路径）
*  __filename：可以看作是 nodejs 中的全局变量，它始终表示当前执行文件的完整文件名(完整绝对路)

我们先在 `path_module` 目录下运行 `node test.js` 命令
```js
<!--当前执行文件的完整路径为\Stone\node\node\path_module\test.js-->
const path = require("path");

console.log(__dirname); // \Stone\node\node\path_module
console.log(__filename); // \Stone\node\node\path_module\test.js
```
然后我们在 `\Stone\node\node` 目录下运行 `node path_module\test.js`，会发现输出结果同上，
所以这就是说明 __dirname 和 __filename 始终跟当前执行文件有关，跟启动脚本所在目录无关。

#### ./ ../
` ./ ` 和  ` ../ ` 我们都知道是相对路径的写法，但是使用的过程中配合 require() 使用与否的结果是不同的。

* fs_module 目录下 执行 node test.js
```
<!--当前启动脚本的执行命令所在目录 \Stone\node\node\fs_module\test.js-->
const fs = require('fs')

fs.readFileSync('./ext/test1.js',(err,data)=> {
    console.log('ok')
})
```
会正常打印出 ok

* \Stone\node\node 目录下 执行 node fs_module\test.js
```
<!--当前启动脚本的执行命令所在目录 \Stone\node\node-->
const fs = require('fs')

fs.readFile('./ext/test1.js',(err,data)=> {
    console.log('ok')
})
```
运行会报错 `no such file or directory, open './ext/test1.js'`

这到底是为啥嘞，原因就是 './' 和 '../' 的路径表示意义需要分情况，当结合 require() 使用的时候相对于当前执行文件，如果不结合 require() 使用的情况下会相对于当前启动脚本的目录，因此只有在 require() 时才使用相对路径(./, ../) 的写法，其他地方一律使用绝对路径，这点一定要注意。

### 结语
关于nodejs path 模块，我们今天就说到这里了，虽然 api 不是很多，但是 path 模块在前端的使用频率还是非常高的，所以觉得很值得学习了解一下的。由于认知有限，本文若有不准确之处还望路过的各位兄台及时指正，吃瓜，吃瓜。

