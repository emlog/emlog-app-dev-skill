# EMLOG 应用开发 Skill

本项目汇集了 EMLOG 博客系统的应用开发知识库（Skill），旨在辅助 AI 编程工具更好地理解和生成 EMLOG 插件与主题代码。

## 包含的 Skill

本项目包含以下两个核心 Skill：

1.  **[Emlog 插件开发 Skill](emlog-plugin-dev-skill/SKILL.md)**
    *   包含插件目录结构、钩子（Hooks）列表、数据库存储操作、常用工具类等开发规范。
2.  **[Emlog 主题开发 Skill](emlog-theme-dev-skill/SKILL.md)**
    *   包含主题目录结构、模板文件说明、常用变量与常量、模板标签等开发规范。

## 使用

### Trae编辑器

将本项目的 Skill 文件添加到 Trae 编辑器的 Skill 目录中

- 插件开发 Skill：/your-project-path/.trae/skills/emlog-plugin-dev-skill
- 主题开发 Skill：/your-project-path/.trae/skills/emlog-theme-dev-skill

### 使用示例

编辑器会根据提示词自动加载相关 Skill 。

提示词：
```
开发一款emlog插件，实现如下功能：
1.  xxxxxxx
2.  xxxxxxx
3.  xxxxxxx
```
