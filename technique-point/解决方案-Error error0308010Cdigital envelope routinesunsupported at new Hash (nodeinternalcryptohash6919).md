# 解决方案 Error: error:0308010C:digital envelope routines::unsupported at new Hash (node:internal/crypto/hash:69:19)

### 1. 控制台报错信息：

```cmd
/usr/local/bin/npm run start

> ant-design-pro@5.2.0 start
> cross-env UMI_ENV=dev umi dev

Browserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
Bundle with webpack 5...
⏱️  MFSU Enabled
Mako https://makojs.dev is a new fast Rust based bundler from us, which is heavily optimized for umi and much faster than webpack. Visit https://makojs.dev/docs/getting-started#bundle-with-umi for more details if you want to give it a try.
🚀 Starting Umi UI using umi@3.5.42...
🌈 Umi UI mini Ready on port 3000.
Starting the development server...

● Webpack █████████████████████████ building (10%) 0/2 entries 1/2 dependencies 0/1 modules 1 active 
 node_modules/@umijs/preset-built-in/bundled/@pmmmwh/react-refresh-webpack-plugin/client/ReactRefreshEntry.js

node:internal/crypto/hash:69
  this[kHandle] = new _Hash(algorithm, xofLen);
                  ^

Error: error:0308010C:digital envelope routines::unsupported
    at new Hash (node:internal/crypto/hash:69:19)
    at Object.createHash (node:crypto:133:10)
    at BulkUpdateDecorator.hashFactory (/Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:184161:18)
    at BulkUpdateDecorator.update (/Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:184062:50)
    at OriginalSource.updateHash (/Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack-sources2/index.js:1:51038)
    at NormalModule._initBuildHash (/Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:115961:17)
    at handleParseResult (/Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:116027:10)
    at /Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:116119:4
    at processResult (/Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:115836:11)
    at /Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:115900:5
    at /Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:35132:3
    at iterateNormalLoaders (/Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:34958:10)
    at Array.<anonymous> (/Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:34949:4)
    at runCallbacks (/Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:24341:15)
    at /Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:24514:4
    at /Users/boyan/DevDir/FrontEndProjects/user-center-frontend-master/node_modules/@umijs/deps/compiled/webpack/5/bundle5.js:33829:16 {
  opensslErrorStack: [ 'error:03000086:digital envelope routines::initialization error' ],
  library: 'digital envelope routines',
  reason: 'unsupported',
  code: 'ERR_OSSL_EVP_UNSUPPORTED'
}

Node.js v18.17.1

Process finished with exit code 0

```



### 2. 定位问题起源：

> 因为 node.js V17版本中最近发布的OpenSSL3.0, 而OpenSSL3.0对允许算法和密钥大小增加了严格的限制



### 3. 解决方案：

#### 3.1 一劳永逸版：降级 Node.js 版本到 17 版本以下

- 需要安装 Node.js 16.x 版本，在终端中运行以下命令：

```bash
# 下载 
nvm install 16
# 切换使用
nvm use 16
# 设置默认使用 16 版本
nvm alias default 16
# 检查当前 Node.js 版本
node -v
```

- 看到当前使用的是 16 版本的 Node.js，回去运行代码，问题解决！
- 通过 nvm ，可以轻松管理和切换不同的 Node.js 版本，以满足不同项目的需求。



#### 3.2 macOS 的 ~/.zshrc 文件中添加：

```bash
# Set OpenSSL legacy provider for Node.js
export NODE_OPTIONS=--openssl-legacy-provider
```

- 可以直接添加到文件顶部；

- 注意：如果安装了 oh-my-zshrc，目录中有两个配置文件：.zshrc.pre-oh-my-zsh 和 .zshrc

  前者是对安装之前的 .zshrc 文件的备份，现在实际起作用的是 .zshrc 文件，所以对这个文件进行修改和更新。

















