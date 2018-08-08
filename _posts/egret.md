### egret技术总结
#### 报错/BUG汇总

##### 小游戏报错
- `TypeError: Cannot read property 'map' of undefined`：这个错误是因为微信开发者工具工程中的game.js文件中使用了如下代码`require('./library/image.js')`而未添加支持库所致

- `showShareMenu:fail no permission`：这个错误是由于创建项目时没有使用真实的appID导致的

- `P is not defined`：这个错误是由于使用了protobuf自动生成的protocol.js中没有将P变量挂载到全局导致，需要在protocol.js定义P之后添加代码`window.P = P`解决。

- 出现闪屏现象：原因是小游戏开放域的帧率与主域的帧率不符合，需要将主域改为60帧。

- 预览时提示json/png等资源丢失：原因是wing发布为微信小游戏时，微信开发者工具也处于打开状态。解决方法是关闭微信开发者工具后再发布，就不会出现打包时资源丢失的提示。（应该是白鹭的问题）

##### wing编辑器BUG
- 将新皮肤文件添加到工程中后，无法立即在皮肤可选列表中出现，需要重新加载项目，具体步骤是菜单栏文件->重新加载

- 调试时提示端口号被占，需要改变端口配置后重新生成，再调试（官方设计有误，应当每次重新调试都释放端口再占用）

##### egret库报错
- res库和assetmanager库的API有差别，会导致代码实际运行效果与文档（文档相当旧了）不符，如果需要将egretProperties.json中的assetmanager改为res，执行egret clean后再执行egret build
