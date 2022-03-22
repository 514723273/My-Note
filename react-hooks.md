## 项目启动

- `create-react-app my-techo --template typescript`；
- 在 `tsconfig.json` 中添加 `"baseUrl": "./src",`，防止引入文件结构复杂 `../../../../xx`；
- 安装配置 prettier（参考[官网](https://prettier.io/docs/en/install.html)：Install、Ignoring Code、Integrating with Linters）（CRA 自带 ESLint）；
- 安装配置 commitlint（参考[官网](https://github.com/conventional-changelog/commitlint)：Getting started；博客参考：[commit规范+commitlint+CHANGELOG自动生成一条龙服务](https://juejin.cn/post/6934292467160514567)）；
  - type 说明：feat: 一项新功能；fix: 一个错误修复；docs: 仅文档更改；style: 不影响代码含义的更改（空白，格式，缺少分号等）；refactor: 既不修正错误也不增加功能的代码更改（重构）；perf: 改进性能的代码更改；test: 添加缺失或更正现有测试；build: 影响构建系统或外部依赖项的更改（gulp，npm等）；ci: 对CI配置文件和脚本的更改；chore: 更改构建过程或辅助工具和库，例如文档生成；

## 常见 Mock 方案对比

1. ❌代码侵入（代码中写入 mock 数据）：切换 server 环境非常麻烦；
2. 请求拦截：
   - 代表： [Mock.js](http://mockjs.com/)；
   - 优点：与前端代码分离、可生成随机数据；
   - 缺点：数据是动态生成的假数据、无法真实模拟增删改查的情况；只支持 Ajax，不支持 Fetch；

3. 接口管理工具：
   - 代表：rap、swagger、moco、yapi；
   - 优点：配置功能强大，接口管理与 mock 一体，后端修改接口 mock 也跟着更改；
   - 缺点：配置复杂，依赖后端；

4. 本地 node 服务器：
   - 代表：[json-server](https://github.com/typicode/json-server)；
   - 优点：配置简单、自定义程度高、增删改查可以真实模拟；
   - 缺点：无法随着后端的修改而自动修改；

## 简单概念

- 状态提升：将页面拆成多个部分，如果一个组件需要另外一个组件的状态，就将这个组件的状态放在父页面，所有子组件都可以共用；

- 手写 useMount、useDebounce；

- 对比函数返回对象和返回 tuple 有什么区别（什么时候函数返回用 tuple）？答：函数返回需重新命名的时候；假如 useState 返回对象，则`const {state: hasButton, setState: setHasButton} = useState(fasle);`，重新命名并不方便；

- unknown 是一个严格版的 any；使用上会有很多限制，比如`u.xxx`是会报错的，但`u === 0`就不会报错，也不能将 unknown 赋值给其他变量；总结，想用 any 的时候就用 unknown；

  

