# 🚴 emlog 应用开发 Agent Skill

协助AI开发 Emlog 应用（插件、主题）。当用户想要创建新应用、修改现有应用、询问 Emlog 应用开发规范时调用的 Agent Skill。

## Agent Skill

本项目包含以下两个核心 Skill：

1.  Emlog 插件开发 Skill
2.  Emlog 主题开发 Skill

- [gitee：emlog 应用开发 Skill](https://gitee.com/snowsun/emlog-app-dev-skill)
- [github：emlog 应用开发 Skill](https://github.com/emlog/emlog-app-dev-skill)

## 使用

### Trae 编辑器

将本项目的 Skill 文件添加到 Trae 编辑器的 Skill 目录中

```
.trae/
└── skills/
    └── emlog-plugin-dev-skill/
    └── emlog-theme-dev-skill/
```

### Cursor 编辑器

将本项目的 Skill 文件添加到 Trae 编辑器的 Skill 目录中

```
.cursor/
└── skills/
    └── emlog-plugin-dev-skill/
    └── emlog-theme-dev-skill/
```

### 提示词

编辑器会根据提示词自动加载相关 Skill，如：

```
开发一款emlog插件，实现如下功能：
1.  xxxxxxx
2.  xxxxxxx
3.  xxxxxxx
```
