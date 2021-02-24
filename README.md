# 开发前请仔细阅读文档

## clone

```
git clone https://github.com/sinsy/vueMultiProject.git
```

## 进入base目录

```
cd base
```

## 添加项目子模块（以tms为例）

```
git submodule add -b dev https://github.com/sinsy/bms.git src/bms
```

## 更新所有子模块

```
git submodule foreach git pull origin dev
```

## 安装yarn

```
sudo npm i yarn -g
```

## 用yarn安装依赖包

```
yarn
```

## 开发模式

```
yarn dev
```

## 本地打包预览

```
yarn start
```

## 项目开发-git操作（以tms为例）

```
cd src/bms //进入项目目录
git checkout dev //切换分支
git add . //添加修改
git commit -m "" //提交代码
git pull origin dev //拉取远程仓库代码
git push origin dev //推送修改到远程仓库
```






# 外部系统植入ERP系统流程

```
git clone git@gitlab.ky-tech.com.cn:erp-frontend/base.git --recursive
```

### 创建模块工程项目
+ 创建自己的git项目（以tms为例）[http://gitlab.ky-tech.com.cn/erp-frontend/bms.git](http://gitlab.ky-tech.com.cn/erp-frontend/bms.git)

+ 在[gitlab](http://gitlab.ky-tech.com.cn)创建一个新项目tms
+ 将创建的新项目作为子模块加入脚手架项目,参考[git子模块文档](https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)

```
git submodule add -b dev git@gitlab.ky-tech.com.cn:erp-frontend/bms.git src/bms
```

+ src目录会生成一个tms目录
在tms目录下开发项目，项目结构参考[http://gitlab.ky-tech.com.cn/erp-frontend/bms.git](http://gitlab.ky-tech.com.cn/erp-frontend/bms.git)

```
├── components
├── pages
│   └── layout.js
├── router
│   └── index.js
└── store
    └── index.js
```

___以上目录和文件为项目必需的，必需的文件必须与参考项目保持一致___

#### 路由注意事项

路由中导入模块的webpackChunkName要统一（一个模块只用一个）

```
const siteMonitorList = () => import(/* webpackChunkName: "coo" */ '../pages/site-monitor/list')
export default [{
  path: 'site-monitor/list',
  component: siteMonitorList,
  meta: { tag: '/coo/site-monitor/list', title: '场地实时监控' }
}]

```

路由数据结构需按一下结构

```
{
  path: 'transfer-loss/list',
  component: transferLossList,
  meta: { tag: '/bms/transfer-loss/list', title: '交接漏扫' }
}
```

___同一个菜单下的路由tag要一致，tag是保证同一个菜单下的页面在同一个标签下打开及返回按钮可以返回列表页___

#### 提交代码，拉取代码等针对项目开发的git操作，需在tms目录下进行

现在整个项目的结构如下

```
└── src
    ├── layout // 公共布局目录
    ├── prints // 打印组件目录
    ├── public // 公共文件目录（子模块）
    ├── router // 路由入口
    ├── bms    // 模块项目开发目录（子模块）
    │    ├── components
    │    ├── pages
    │    │   └── layout.js
    │    ├── router
    │    │   └── index.js
    │    └── store
    │        └── index.js
    ├── app.vue   // 跟组件
    ├── main.js   // 项目入口
    ├── lookup.json  // 数据字典mock数据
    └── menus.json   // 菜单mock数据
```

___lookup.json、menus.json中的数据结构必须与范例保持一致___

#### 更新脚手架项目在最外层目录进行，更新public项目在public目录进行

#### 子模块项目分支名约定

+ ```master``` 生产分支
+ ```test``` 测试分支
+ ```dev``` 开发分支

___其他分支可根据需要自行创建___

#### menus.json & lookup.json 配置说明

+ menus

属性 | 类型 | 说明
---|---|---
id | string | 主键，要求唯一
parentId | string | 父级菜单的主键，要求唯一
menuCode | string | 菜单对应的前端路由
menuTitle | string| 菜单标题
subSystem | string | 系统名称，数据字典：auth_menu_resource_sub_system，[CRM,TMS,COO,VMS,FMS,HRMS,OAMS,PMS,IMS,RBAC,SYS]
childrenMenuResources | array | 子菜单

+ lookup

属性 | 类型 | 说明
---|---|---
code | string | 数据字典编码，要求唯一
description | string | 字典的描述文本
childrenLookupValues | array | 字典的子项
lookupCode | string | 传给后台的value
lookupValue | string | 显示在前端的lable

### 接口相关规范

+ （通用查询、自定义查询、列表）入参

```
{
  "page": 1,
  "pageSize": 20,
  "elasticsearchFlag": "Y",
  "generic": {
    "vos": [
      {
        "propertyName": "order.goodsTime",
        "columnName": "om.goods_time",
        "frontBrackets": "(",
        "postBrackets": ")",
        "conditionOperation": "and",
        "operation": "between",
        "type": "date",
        "values": [
          "2018-08-01 00:00:00",
          "2018-08-01 23:59:59"
        ]
      },
      {
        "columnName": "om.exception_flag",
        "propertyName": "order.exceptionFlag",
        "frontBrackets": "(",
        "postBrackets": ")",
        "conditionOperation": "",
        "operation": "contain",
        "type": "enum",
        "values": [
          "20"
        ]
      }
    ]
  }
}
```

*命名大小写要一致，如果是普通查询除page、pageSize外其他参数可根据需要自定义*


+ （通用查询、自定义查询、列表）出参

```
{
  "code": 0,
  "msg": "OK",
  "data": {
    "page": 1,
    "pageSize": 20,
    "pageTotal": 2,
    "rowTotal": 24,
    "rows": [
      {
        "airportName": "测试机场123456",
        "airportCode": "SZW",
        "regionCode": "20",
        "hotLevel": null,
        "serialNumber": null,
        "longitude": 3.0,
        "latitude": 3.0,
        "provinceId": "",
        "address": "广东省深圳宝安区草草草草",
        "remark": "备注123",
        "id": "984725975980904448",
        "updatedBy": "958643378213425151",
        "updationDate": 1530498443000
      }    
    ]
  }
}
```

*命名大小写要一致，rows、rowTotal 必须要有*

___其他接口规范请按openapi的规范___


+ 调用开放平台的接口使用框架封装好的this.$http
+ 调用其它接口请使用axios自行封装实例，注意不要覆盖$http

#### 浏览器兼容注意事项
+ 目前只做chrome浏览器的兼容适配
+ 默认babel配置 最低兼版本为chrome55
+ 如需兼容更低版本的chrome，请自行根据需求修改babel配置

