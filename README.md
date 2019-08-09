# 魅族官网国际化方案 react SSR 重构(react 服务器渲染)
### 说明
本项目是 **[魅族官网国际化解决方案 [i18n-www](http://gitlab.meizu.com/www/i18n-www)]**的重构优化。 

为了满足高性能渲染、SEO（静态化）、国际化、可维护性、良好的开发体验、高效的部署流程等需求。同时，需要满足运营需求，即动态更新网站内容。

结合以上，本项选用 react 技术栈，基于 [next.js](https://github.com/zeit/next.js) 开发并结合 [DMS系统](https://dms.meizu.com) 实现**可静态化的内容管理**。

项目大致包含的技术栈如下：
- es6
- react
- next.js
- webpack
- koa2
- redux
- sass
- i18next
- axios / jsonp
- gulp

### 安装
**- 请先确保 node 环境为 v8.11.4**

```
cd ~/meizu/fed/         #存放项目的目录,可自定义
git clone gitlab@github.meizu.com:seventeam-fe/i18n-react-www.git
cd i18n-react-www
npm install             #安装项目本地 node_modules 依赖模块

```

### 配置相关环境变量

由于本项目沿用了就项目的静态资源发布方式，故而需要配置 `MZ_FIS_MANAGE_SECRET` 环境变量，用作发布灰度和外网静态资源的密匙。

```
# 编辑 ~/.bash_profile 文件

# 具体值请询问团队成员
export MZ_FIS_MANAGE_SECRET='test'

# 修改完成后保存退出，并执行该文件
source ~/.bash_profile

```

### 目录结构
```
|-- .next/              # 编译生成的文件夹

|-- assets/             # assets文件夹用于存放需要通过webpack编译的资源，如scss、es写法的js
    |-- header/
    |   |-- header.scss
    |-- plugin/
    |   |-- mz-scroll.js
    |-- util/
    |   |-- helper.js
    ...
    
|-- bin/                # 工作流相关，执行发布资源、编译、静态化等脚本
    |-- deploy.js
    |-- export_www.sh
    |-- set_build_id.sh
    ...
    
|-- components/         # react 组件模块
    |-- footer/
    |   |-- Footer.js
    |   |-- FooterLink.js
    |-- header/
    |   |-- Header.js
    |   |-- HeaderNav.js
    ...

|-- config/             # 配置和定制化
    |-- customPathMap.js # 路由优化和定制
    |-- dmsDataMap.js    # dms系统的 blockId 配置
    |-- VERSION_ID       # 版本号，通常不需要手动维护
    ...
    
|-- lang/                # 国际化配置，一些不需要运营维护的国际化配置，可以在这设置
    |-- cn
    |   |-- common.js
    |-- en
    |   |-- common.js
    ...
 
|-- node_modules/        # 项目依赖
|-- out/                 # export 静态化出来的文件

|-- page/                # 页面，默认目录结构与项目路由一致
    |-- m6/
    |   |   |-- summary/
    |   |   |   |-- index.js # m6 概述页
    |-- index.js         # 首页
    ...
    
|-- static/              # 静态资源，通常存放不经过webpack处理的图片和es5 js依赖库

|-- store/               # redux 以及 api 接口
    ...
|-- _deploy_map.json     # 用户优化静态资源发布的map表
|-- .babelrc             # es6 babel 配置
|-- .eslintrc.js         # eslint 配置
|-- .stylelintrc.json    # stylelint 配置
|-- ecosystem.config.js  # pm2 配置
|-- gulpfile.js          # gulp 配置，主要用来压缩优化图片
|-- next.config.js       # next 配置，版本号、路由、webpack
|-- package.json         
|-- server.js            # node 服务入口
```

### 开发流程简图
![](https://fms.res.meizu.com/dms/2019/03/11/4d5856f4-3beb-4a5f-87a6-e5680972aeab.jpg)

    【注意】
    1.由于 `npm run update` 会更新 config/VERSION_ID 文件，并且远程服务器端的 build 操作需要根据该文件来同步版本号。
    所以要先 `npm run update`，再 `git commit` 提交版本。

    2.静态资源的推送，需要确保 i18n.meizu.com 和 i18n-test.meizu.com 的host 配置正确。

####本地开发
 
```
npm run dev
```
注意：
本地开发，也可以在本地调试测试或者灰度环境的代码。


1. cd 到目录

```
npm run build  # 以 NODE_ENV = production 编译
npm start      # 开启本地 node 服务，默认3000端口 

```

2. 切换 hosts

#### hosts
测试环境：

```
172.16.180.77 m2.res.meizu.com  cimg.res.meizu.com www2.res.meizu.com www3.res.meizu.com retail2.res.meizu.com
172.16.180.77 care.res.meizu.com
172.16.180.75 retail.meizu.com
172.16.180.75 www.meizu.com m.meizu.com www-test.meizu.com
```

灰度环境：

```
101.201.114.32 m.meizu.com www.meizu.com www-test.meizu.com
123.57.210.228 care.meizu.com
```

发布平台：

```
# 测试发布平台
172.16.180.77 i18n.jenkins.com

# 灰度 & 外网发布平台
120.76.237.51 jenkins.bbs.meizu.cn
```


