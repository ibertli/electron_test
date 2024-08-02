node 20.15.0
npm 10.7.0
electron 31.3.1

## 执行步骤

1. 初始化

   ```
   npm init
   ```

   注意：entry point 应为 main.js，author 与 description必填

2. 安装应用包：将 electron 包安装到应用的开发依赖中

   ```
   npm install --save-dev electron
   ```

   如果在安装时报错，可以尝试如下方法：

   ```
   npm install -g cnpm --registry=https://registry.npmmirror.com
   cnpm install --save-dev electron
   ```

3. 理解主进程：任何electron应用程序的入口都是main文件，它控制了主进程，主进程运行在一个完成的Node.js环境中，负责控制应用的声明周期（激活、关闭），显式原生界面、执行特殊操作、
并管理渲染进程（这又是另外一个进程）。

4. 在根目录下，创建main.js和index.html. 为了将html页面加载进应用窗口中，需要在js中加载两个electron模块

   ```
   const { app, BrowserWindow } = require('electron')
   ```

   然后，添加一个createWindow()方法来将index.html加载进一个新的BrowserWindow实例

   ```
   const createWindow = () => {
   const win = new BrowserWindow({
    width: 800,
    height: 600
   })

   win.loadFile('index.html')
   }

   app.whenReady().then(() => {
      createWindow()
   })
   ```

5. 获取版本号：在主进程中（package.json中‘main’参数定义的那个js文件）可以通过全局的process对象访问版本信息，但是得到版本值之后，无法在主进程中编辑dom，因为它无法访问渲染器文档
的上下文（注意：这里引出了一个渲染器文档）

6. 预加载脚本：预加载脚本在渲染器进程加载之前加载，并有权访问两个渲染器全局（window和document）和Node.js环境,创建preload.js文件，在创建浏览器窗口时添加

   ```
   const mainWindow = new BrowserWindow({
      width: 800,
      height: 600,
      webPreferences:{
         preload: path.join(__dirname, 'preload.js')
      }
   })
   ```

7. 使用Electron Forge来打包并分发应用程序:将 Electron Forge 添加到您应用的开发依赖中，并使用其"import"命令设置 Forge 的脚手架

   ```
   npm install --save-dev @electron-forge/cli
   npx electron-forge import
   ```

   如果在执行上述命令时报git相关的错误，可以在git bash环境下执行这个命令



