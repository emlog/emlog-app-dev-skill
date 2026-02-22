---
name: emlog-theme-dev
description: 协助开发 Emlog 主题。当用户想要创建新主题、修改现有主题、询问 Emlog 主题开发规范或查找模板变量时调用。
---

# Emlog 主题开发助手

此 Skill 旨在帮助用户开发 Emlog 主题，包含最新的开发规范和接口文档。
参考文档：https://www.emlog.net/docs/dev/template

## 1. 目录结构与文件说明

模板文件位于 `content/templates` 目录下。每个模板是一个独立的文件夹，以英文别名命名。

| 文件名         | 说明         | 备注                               |
| :------------- | :----------- | :--------------------------------- |
| `header.php`   | 站点头部信息 | 包含页面head信息、顶部标题、导航栏 |
| `echo_log.php` | 文章详情页   | 展示单篇文章内容                   |
| `log_list.php` | 首页/列表页  | 展示文章列表                       |
| `footer.php`   | 站点底部信息 | 展示版权信息等                     |
| `page.php`     | 自定义页面   | 展示自定义的页面                   |
| `side.php`     | 侧边栏       | 非必须                             |
| `module.php`   | 功能模块     | 定义侧边栏组件、自定义函数等       |
| `404.php`      | 404页面      | 页面未找到时的报错页面             |
| `preview.jpg`  | 预览图       | 500x300 jpg格式                    |
| `options.php`  | 模板设置     | 模板后台设置选项（非必须）         |
| `css/`         | 样式目录     | 存放 CSS 文件                      |
| `js/`          | 脚本目录     | 存放 JS 文件                       |
| `images/`      | 图片目录     | 存放图片资源                       |

## 2. 核心开发规范

### 防止直接访问
每个 PHP 文件开头必须包含以下代码，防止被直接访问：
```php
<?php
if(!defined('EMLOG_ROOT')) {exit('error!');}
?>
```

### 模板信息注释 (header.php)
`header.php` 开头必须包含模板信息注释：
```php
/*
Template Name:我的主题
Version:1.0
Template Url:https://www.emlog.net/template/
Description:这是一个 Emlog 主题
Author:作者名
Author Url:https://www.emlog.net/author/index/1
*/
```

### 引用模板文件
在模板中引用其他模板文件（如 header, footer, side）：
```php
<?php
// View::getView('文件名','文件后缀')将返回当前模板安装路径下对应的文件。
require_once View::getView('header');
require_once View::getView('side');
require_once View::getView('footer');
?>
```

## 3. 常用变量与常量

### 全局常量
- `BLOG_URL`: 站点首页 URL (e.g., `https://emlog.net/`)
- `TEMPLATE_URL`: 当前模板文件夹 URL (e.g., `http://emlog.net/content/templates/default/`)

### header.php 常用变量
- `$site_title`: 站点标题
- `$site_key`: 站点关键字
- `$site_description`: 站点描述
- `$blogname`: 博客名称
- `$bloginfo`: 博客副标题

### log_list.php (文章列表) 常用变量
- `$value['logid']`: 文章 ID
- `$value['log_title']`: 文章标题
- `$value['log_url']`: 文章链接
- `$value['log_description']`: 文章摘要
- `date('Y-n-j', $value['date'])`: 发布时间
- `$value['comnum']`: 评论数
- `$value['views']`: 浏览量
- `$value['log_cover']`: 封面图 URL

### echo_log.php (文章详情) 常用变量
- `$logid`: 文章 ID
- `$log_title`: 文章标题
- `$log_content`: 文章内容
- `$date`: 发布时间 (时间戳)
- `$author`: 作者 ID
- `$sortid`: 分类 ID
- `$log_cover`: 封面图 URL

## 4. 模板引擎
Emlog 使用原生 PHP 作为模板引擎。

- **输出变量**: `<?= $value ?>`
- **循环**: `<?php foreach ($logs as $value): ?> ... <?php endforeach; ?>`
- **判断**: `<?php if ($condition): ?> ... <?php endif; ?>`

## 5. 最佳实践

- PHP 版本适配：代码需兼容 PHP 7.4 及 PHP 8.1

## 参考文档

emlog主题开发文档：./template.md
