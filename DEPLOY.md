# OP Plugin 发布手册

## 版本号规范

采用语义化版本 `MAJOR.MINOR.PATCH`（不带 v 前缀）：
- **格式**: `1.0.0`（主版本.次版本.修订号）
- **示例**: `1.0.0`、`1.1.0`、`1.0.1`
- **标签策略**: 精确版本 + `latest`（两层标签，简单清晰）

## 版本管理说明

**版本号唯一来源**：`.claude-plugin/plugin.json` 中的 `version` 字段

```json
{
  "name": "op-plugin",
  "version": "1.0.0"  // ← 唯一需要修改的地方
}
```

**环境变量设置**（自动读取版本号）：

```bash
# 从 plugin.json 自动读取版本号并设置到 $VERSION，并打印输出
export VERSION=$(python3 -c "import json; print(json.load(open('.claude-plugin/plugin.json'))['version'])")
echo "当前版本: $VERSION"
```

> **注意**：不要手动设置 `export VERSION=1.0.0`，应该始终从 plugin.json 自动读取，确保版本一致性

## 发布流程

### 1. 更新版本号

编辑 `.claude-plugin/plugin.json`，更新 `version` 字段：

```json
{
  "name": "op-plugin",
  "version": "1.0.1"  // 更新版本号
}
```

### 2. 提交更改

```bash
# 提交版本更新
git add .claude-plugin/plugin.json
git commit -m "chore: bump version to ${VERSION}"

# 推送到远程
git push origin main
```

### 3. 创建 Git 标签

```bash
# 自动读取版本号
export VERSION=$(python3 -c "import json; print(json.load(open('.claude-plugin/plugin.json'))['version'])")

# 创建标签
git tag -a "v${VERSION}" -m "Release version ${VERSION}"

# 推送标签
git push origin "v${VERSION}"
```

### 4. 验证发布

```bash
# 查看标签
git tag -l

# 查看特定版本
git show v${VERSION}
```

## 在其他项目中使用

### 方式一：Git Submodule（推荐）

```bash
# 在目标项目中添加 submodule
git submodule add https://github.com/your-org/op-plugin.git plugins/op-plugin

# 使用特定版本
cd plugins/op-plugin
git checkout v1.0.0
cd ../..
```

### 方式二：直接克隆

```bash
# 克隆到本地
git clone https://github.com/your-org/op-plugin.git plugins/op-plugin

# 切换到特定版本
cd plugins/op-plugin
git checkout v1.0.0
```

### 方式三：在 SDK 中使用

```python
from claude_agent_sdk import ClaudeAgentOptions

options = ClaudeAgentOptions(
    plugins=[
        {
            "type": "local",
            "path": "./plugins/op-plugin"
        }
    ]
)
```

## 目录结构

发布后的 plugin 结构：

```
op-plugin/
├── .claude-plugin/
│   └── plugin.json              # 插件清单
├── agents/                      # Agents
│   ├── designer.md
│   ├── developer.md
│   ├── op_button.md
│   ├── op_connector.md
│   ├── op_event.md
│   └── op_workflow.md
└── skills/                      # Skills
    ├── op_db/
    │   └── SKILL.md
    ├── op_db_metadata/
    │   └── SKILL.md
    ├── op_db_openapi/
    │   └── SKILL.md
    └── op_db_user/
        └── SKILL.md
```

## 发布检查清单

- [ ] 更新 `.claude-plugin/plugin.json` 中的版本号
- [ ] 确保所有 agents 和 skills 文件格式正确
- [ ] 提交更改到 Git
- [ ] 创建 Git 标签
- [ ] 推送代码和标签到远程仓库
- [ ] 验证标签创建成功

