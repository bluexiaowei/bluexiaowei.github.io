# 前端文件上传（一）

## 前言

一个简单上传实例已经无法满足前端业务场景的复杂业务需求。如：秒传、断点、暂停和高速等功能。
为了以最简单方式实现功能，博主还是引入第三方工具来实现（其实是个人能力不足 😿）。
本章节分前后端两部分：

- WEB 前端：jQuery、WebUplader
- WEB 后端：Node、Express

### 设计

### WEB 前端

#### 前端-流程设计

![前端上传流程图](../static/01-01.png '前端上传流程图')

#### 前端-目录结构

```bash
.
├── package.json
├── src
│   ├── index.html
│   └── script
│       └── index.js
└── yarn.lock
```

#### 前端-项目依赖

```json
{
  "name": "web_uploader_demo",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "start": "npx parcel ./src/index.html"
  },
  "dependencies": {
    "jquery": "^3.3.1",
    "webuploader": "^0.1.8"
  },
  "devDependencies": {
    "parcel-bundler": "^1.11.0"
  }
}
```

从现在让我们开始吧！！！`npm start`

#### 前端-页面结构

```html
<!-- src/index.html --->
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0, user-scalable=0"
    />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>web_uploader_demo</title>
  </head>
  <body>
    <!--dom结构部分-->
    <div id="uploader-demo">
      <!--用来存放item-->
      <div id="fileList" class="uploader-list"></div>
      <div id="filePicker">选择图片</div>
    </div>

    <script type="text/javascript" src="script/index.js"></script>
  </body>
</html>
```

#### 前端-逻辑部分

首先创建一个上传容器

```javascript
// src/script/index.js
import $ from 'jquery';
import WebUploader from 'webuploader';
import 'webuploader/css/webuploader.css';

// 创建上传容器
const uploader = WebUploader.create({
  server: 'http://localhost:3000/upload', // 服务器路径
  pick: '#filePicker', // 上传容器 DOM
  chunked: true, // 是否切片
  chunkSize: 512 * 1024, // 切片大小
  auto: true, // 自动上传
  compress: false, // 文件不要压缩
  accept: {
    // 限制文件类型
    title: 'Images',
    extensions: 'gif,jpg,jpeg,bmp,png',
    mimeTypes: 'image/*'
  }
});
```

如果你的页面中生成一个 **选择图片** 的按钮，恭喜你可以上传了哦（需要服务配合哦）。

接下来可以在此基础上添加额外的功能。如：秒传。
在创建容器前面注册（hooks）钩子。

```js
// src/script/index.js
// ...

// 注册 hooks （钩子）
WebUploader.Uploader.register(
  { 'before-send-file': 'preupload' },
  { preupload }
);

// 创建上传容器
// ...

// 添加监听事件
// 当某个文件的分块在发送前触发，主要用来询问是否要添加附带参数，大文件在开起分片上传的前提下此事件可能会触发多次。
uploader.on('uploadBeforeSend', (block, sendData, headers) => {
  // 扩展上传字段
  sendData.hash = block.file.hash;
});

// 文件上传前执行的函数
function preupload(file) {
  const deferred = WebUploader.Deferred();
  this.owner
    .md5File(file.source)
    .then(md5 => {
      // 获取 md5 成功后执行
      // 将计算出来的 md5 值，挂载在文件属性上（为后面做铺垫）
      file.hash = md5;
      // 服务端验证是否存在该文件
      $.ajax({
        method: 'POST',
        url: 'http://localhost:3000/upload/check',
        data: { hash: md5 },
        success: data => {
          // 服务器校验成功
          if (data.success && data.data.repeat) {
            // 标记文件已经上传，将不再调用上传 API
            this.owner.skipFile(file);
          }
          deferred.resolve();
        }
      });
    })
    .fail(() => {
      // 读取文件 md5 失败
      deferred.reject();
    });

  return deferred.promise();
}
```

在文件开始将先执行 `preupload` 如果服务器告诉我们:

- 文件已经存在 - 则判断文件上传成功，跳过文件上传。
- 文件不存在 - 继续执行 `uploadBeforeSend` 将文件 hash 添加到上传字段中并上传文件。

OK 秒传结束。

继续添加功能： 切片（用于快速上传和断点上传）。

让我们继续注册钩子

```javascript
// src/script/index.js
// ...

// 注册 hooks （钩子）
WebUploader.Uploader.register(
  {
    'before-send-file': 'preupload',
    'before-send': 'checkchunk'
  },
  { preupload, checkchunk }
);

// 创建上传容器
// ...

// 添加监听事件

uploader.on('uploadBeforeSend', (block, data, headers) => {
  data.hash = block.file.hash;
  data.chunk_hash = block.chunk_hash;
});
// 上传成功后的回调 用于合并分片信息。
uploader.on('uploadSuccess', function(file) {
  $.ajax({
    method: 'post',
    url: 'http://localhost:3000/upload/merge',
    data: { hash: file.hash, name: file.name },
    success: data => {
      console.log('上传成功');
    }
  });
});

// 文件上传前执行的函数
// ...

// 切片上传前执行的函数
function checkchunk(block) {
  const deferred = WebUploader.Deferred();
  this.owner
    .md5File(block.blob)
    .then(md5 => {
      // 基本同 preupload 一个逻辑。
      block.chunk_hash = md5;

      $.ajax({
        method: 'POST',
        url: 'http://localhost:3000/upload/check',
        data: { hash: md5, chunk: true },
        success: data => {
          if (data.success && data.data.repeat) {
            // 跳过该切片
            deferred.reject();
          } else {
            deferred.resolve();
          }
        }
      });
    })
    .fail(() => {
      deferred.reject();
    });
  return deferred.promise();
}
```

好了，亲！
我们的所有基础代码都已经写完了，你可以在此基础上实现很多不可思议的内容。（建议去 Web uploader 官方网站查看跟多功能。连接在最下面 👇）

### WEB 后端

😫 好累，不过学习使我们有无限的动力。

#### 后端-流程设计

![后端文件接收流程图](./static/01-02.png '后端文件接收流程图')

#### 后端-目录结构

这是用 express cli 创建出来的 `static`、`temp`。

```bash
.
├── app.js
├── bin
├── package.json
├── public
├── routes
│   ├── index.js
│   └── users.js
├── views
└── yarn.lock
```

#### 后端-项目依赖

```json
{
  "name": "service_uploader_demo",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "npx nodemon ./bin/www"
  },
  "dependencies": {
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.9",
    "express": "~4.16.0",
    "http-errors": "~1.6.2",
    "jade": "~1.11.0",
    "morgan": "~1.9.0",
    "multer": "^1.4.1"
  },
  "devDependencies": {
    "nodemon": "^1.18.10"
  }
}
```

从现在让我们开始吧！！！`npm start`

由于两个服务都在本地启动，说以占用两个端口。这样浏览器会启用安全策略造成**跨域问题**：解决如下

```javascript
// routes/index.js
const fs = require('fs');
const path = require('path');
const express = require('express');
const multer = require('multer');
const router = express.Router();
const tempFile = multer({ dest: 'temp/' });

// 处理本地开发的跨域问题
router.all('*', (req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Headers', 'X-Requested-With');
  res.header('Access-Control-Allow-Methods', 'PUT,POST,GET,DELETE,OPTIONS');
  res.header('X-Powered-By', ' 3.2.1');
  res.header('Content-Type', 'application/json;charset=utf-8');
  next();
});
```

现在我们来整理思路实现三个 API 接口：

1. 实现文件上传（切片文件/不是切片的文件）。
2. hash 判断文件或切片是否存在。
3. 如果文件上传完整合并分片。

第一个 API `POST http://localhost:3000/upload`

```javascript
// routes/index.js
// 引入依赖...
// 处理跨域...

// POST /upload 接收文件
router.post('/upload', tempFile.any(), function(req, res) {
  const { hash, chunks, chunk, chunk_hash } = req.body;
  const [file] = req.files;
  let des_path = '';
  // 判断是切片信息
  if (chunk) {
    // 重命名切片文件名 `${file.destination}/${chunks}_${chunk}_${hash}_${chunk_hash}`
    des_path = `${file.destination}/${chunks}_${chunk}_${hash}_${chunk_hash}`;
  } else {
    // 重命名文件名 `${file.destination}/${hash}`
    des_path = `${file.destination}/${hash}`;
  }
  fs.rename(file.path, des_path, function(err) {
    if (err) {
      res.json({ success: true, message: '上传失败' });
      console.log(err);
    }
    res.json({ success: true, message: '上传成功' });
  });
});
```

第二个 API `POST http://localhost:3000/upload/check`

```javascript
// routes/index.js
// 引入依赖...
// 处理跨域...
// POST /upload...

// POST /upload/check 验证文件是否存在
router.post('/upload/check', function(req, res) {
  const { chunk, hash } = req.body;

  if (chunk) {
    // 切片验证
    const fileNameList = fs.readdirSync('temp');

    if (fileNameList.some(fileName => fileName.includes(hash))) {
      // 说明切片存在
      res.json({ success: true, data: { repeat: true } });
    } else {
      // 说明切片不存在
      res.json({ success: true, data: { repeat: false } });
    }
  } else {
    // 文件验证
    const fileNameList = fs.readdirSync('static');

    if (fileNameList.some(fileName => fileName.includes(hash))) {
      res.json({ success: true, data: { repeat: true } });
    } else {
      res.json({ success: true, data: { repeat: false } });
    }
  }
});
```

第三个 API

```javascript
router.post('/upload/merge', function(req, res) {
  const { hash, name } = req.body;
  const extname = path.extname(name); // 获取文件扩展名
  const filePublishPath = `static/${hash}${extname}`; // 合并后的路径
  const isExists = fs.existsSync(filePublishPath); // 合并后的文件是否存在

  // 如果旧文件存在
  if (isExists) {
    // 删除旧文件
    fs.unlinkSync(`filePublishPath`);
  }

  // 读取临时文件夹下面的说有文件
  const tempChildrenfileNameList = fs.readdirSync('temp');
  // 过滤不相关的文件名
  const fileNameList = tempChildrenfileNameList.filter(fileName =>
    fileName.includes(hash)
  );
  const [fileName] = fileNameList;

  if (fileNameList.length > 1) {
    // 存在文件切片
    // 对文件进行排序
    fileNameList.sort((a, b) => {
      const [, aIndex] = a.split('_');
      const [, bIndex] = b.split('_');
      return Number(aIndex) - Number(bIndex);
    });

    const [firstFileName] = fileNameList;
    // 获取拼成文件需要的总数
    const [total] = firstFileName.split('_');

    if (fileNameList.length == total) {
      // 切片齐全可以合并
      fileNameList.forEach(fileName => {
        // 读取分片追加到新文件中
        fs.appendFileSync(filePublishPath, fs.readFileSync(`temp/${fileName}`));
        // 删除切片
        fs.unlinkSync(`temp/${fileName}`);
      });
      res.json({ success: true, name, path: filePublishPath });
    } else {
      res.json({ success: false, message: '文件不完整' });
    }
  } else if (fileName && !fileName.includes('_')) {
    // 存在文件并且不是分片信息
    fs.rename(`temp/${hash}`, filePublishPath, function(err) {
      if (err) {
        res.json({
          success: true,
          name: name,
          path: filePublishPath,
          message: '合并失败'
        });
        console.log(err);
      }
      res.json({
        success: true,
        name: name,
        path: filePublishPath,
        message: '合并成功'
      });
    });
  } else {
    res.json({ success: true, message: '没有文件信息' });
  }
});
```

报告！本文结束。

## 总结

博主能力有限，经常会出现一些小错误 ☹️。欢迎各位评点指正 😄。

### 相关资料

- [jQuery JavaScript Library](https://jquery.com/)
- [Node 基于 Chrome V8 引擎 的 JavaScript 运行环境](https://nodejs.org/zh-cn/)
- [百度前端 Web Uploader 组件官方地址](http://fex.baidu.com/webuploader)
- [Express 是基于 Node 的 WEB 开发框架](http://www.expressjs.com.cn/starter/generator.html)
- [楼主百度搜索 webuploader express 借鉴的博客](https://blog.csdn.net/xiazhiqiang01/article/details/52677484)
