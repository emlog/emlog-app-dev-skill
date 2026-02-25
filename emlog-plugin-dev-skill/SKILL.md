---
name: "emlog-plugin-dev"
description: "协助创建和迭代 Emlog 插件。当用户想要创建新插件、修改迭代插件时调用。"
---

# Emlog 插件开发助手

此 Skill 旨在帮助您开发 Emlog 插件，包含最新的开发规范和接口文档。

## 插件结构与规范

### 目录结构
插件位于 `content/plugins/<plugin_alias>/` 目录下。

| 文件名                        | 说明                                                             |
| :---------------------------- | :--------------------------------------------------------------- |
| `<plugin_alias>.php`          | 核心主文件。包含插件元数据（Header）和钩子注册。                 |
| `<plugin_alias>_callback.php` | 生命周期回调。定义激活(`init`)、更新(`up`)、删除(`rm`)时的逻辑。 |
| `<plugin_alias>_setting.php`  | 后台设置页。包含 `plugin_setting_view` 函数，管理员可见。        |
| `<plugin_alias>_show.php`     | 前台页面。用于构建前台独立页面（如 `/?plugin=name`）。           |
| `languages/`                  | 多语言包。如 `zh_CN/main.php`, `en_US/main.php`。                |
| `preview.jpg`                 | 预览图。75x75 像素，用于后台列表展示。                           |

### 命名规范
- 插件别名：只能包含小写字母、数字、下划线、横杠，且以字母开头（如 `my_tool`）。
- 函数命名：必须使用插件别名作为前缀（如 `my_tool_func`），防止冲突。
- 安全检查：所有 PHP 文件头部必须包含：
  ```php
  defined('EMLOG_ROOT') || exit('access denied!');
  ```

## 数据存储

使用 `Storage` 类存储键值对配置，禁止使用写文件的方式保存配置。

```php
$storage = Storage::getInstance('my_plugin');

// 写入 (支持 string, number, boolean, array)
$storage->setValue('my_key', 'some value');
$storage->setValue('my_config', ['a' => 1], 'array');

// 读取
$val = $storage->getValue('my_key');

// 删除
$storage->deleteName('my_key');
$storage->deleteAllName('YES'); // 删除该插件所有数据
```

## 输入输出

### Input

使用 `Input` 类获取 GET/POST 变量，禁止直接访问 `$_GET`/`$_POST` 以防止 SQL 注入。

```php
// 获取字符串 (默认值为空)
$str = Input::postStrVar('name', '');
$str = Input::getStrVar('name', '');

// 获取整数 (默认值为 0)
$int = Input::postIntVar('id', 0);
$int = Input::getIntVar('id', 0);

// 获取数组
$ids = Input::postIntArray('ids'); // 数字数组
$tags = Input::postStrArray('tags'); // 字符串数组

// 混合获取 (GET/POST/COOKIE)
$val = Input::requestStrVar('key', 'default');
```

### Output

使用 `Output` 类返回 JSON 格式数据，确保 API 响应一致，避免使用echo输出json。

```php
Output::ok(); // 返回成功 JSON
Output::ok(['id' => 1]); // 返回带数据的成功 JSON
Output::error('Permission denied'); // 返回错误 JSON
```

### 常用函数

- `Option::get('att_maxsize')`: 获取系统配置。
- `Notice::sendMail($to, $title, $content)`: 发送邮件。
- `subContent($content, 180, 1)`: 截取内容（第3个参数为1时过滤HTML）。
- `getFirstImage($content)`: 获取内容中的第一张图片 URL。
- `smartDate($timestamp)`: 返回“1分钟前”等友好时间格式。

## 其他最佳实践

- PHP 版本适配：代码需兼容 PHP 7.4 及 PHP 8.1
- 生命周期管理：在 `_callback.php` 中实现 `callback_init`（初始化数据）、`callback_rm`（清理数据）。
- 绿色插件：不要修改系统核心表；卸载时务必清理所有自建数据。

## 挂载点 (Hooks) 参考

使用 `addAction('hook_name', 'function_name')` 注册钩子。

### 常用后台挂载点
| 挂载点              | 描述                                | 参数                          |
| :------------------ | :---------------------------------- | :---------------------------- |
| `adm_head`          | 后台 `<head>` 区域，用于加载 CSS/JS | 无                            |
| `adm_footer`        | 后台底部，用于加载 JS               | 无                            |
| `adm_main_top`      | 后台仪表盘顶部                      | 无                            |
| `save_log`          | 保存文章时触发                      | `$blogid, $pubPost, $logData` |
| `del_log`           | 删除文章时触发                      | `$blogid`                     |
| `adm_writelog_head` | 写文章页摘要下方                    | 无                            |
| `adm_writelog_side` | 写文章页右侧栏下方                  | 无                            |

### 常用前台挂载点
| 挂载点          | 描述                       | 参数       |
| :-------------- | :------------------------- | :--------- |
| `index_head`    | 前台 `<head>` 区域         | 无         |
| `index_footer`  | 前台底部                   | 无         |
| `log_related`   | 文章详情页相关区域         | `$logData` |
| `comment_post`  | 评论提交前（可用于反垃圾） | 无         |
| `comment_saved` | 评论保存后（可用于通知）   | 无         |

### 高级挂载类型
- doAction (插入式): 顺序执行所有挂载函数（默认）。
- doOnceAction (单次接管): 仅执行第一个挂载函数，用于替换系统核心功能（如 `upload_media`）。
- doMultiAction (轮流接管): 管道式处理，前一个函数的输出作为下一个的输入（如 `article_content_echo`）。

## 参考文档

emlog插件完整开发文档：./plugin.md
