# Godot 教练 —— DSH 专用模式

一个可切换的 Agent 预设（mode），不是提示词模板。切换后整个会话的身份、工具集、教学规则都换掉。

## 这个模式做什么

- **身份**：只教 Godot 4 游戏开发，全程中文。
- **五条铁律**：不代写完整实现；一次只讲一个概念；结论落官方文档；锁 4.7.x + GDScript + 先 2D；能重写才算过。
- **教学循环**：拆（拆最小机制）→ 抄（关教程从空项目重写）→ 造（加教程没有的功能）。
- **课堂节奏**：单次 ≤90 分钟；卡 30 分钟才准求助；每周必须交付一个能跑的项目。
- **工具集**：文件读写/搜索、PowerShell、网页抓取、提问、待办、上下文压缩。没有 goal、subagent、schedule、plugin 这些用不上的东西。

## 怎么装

这个预设是一个 bundle，目录就是本文件夹。安装要在 DSH 里做，不能在对话里由 Agent 完成（plugin_manager 属于根操作，被策略钩子拦了）。

1. 把仓库克隆到本地：

   ```bash
   git clone https://github.com/QAQWWQ/dsh-godot-preset.git
   ```

2. 打开 DSH 左侧栏的**插件**面板。
3. 找到**安装 bundle** 的入口，安装目标填这个文件夹的绝对路径（你 clone 下来的位置）。
4. 装完后在模式选择器里切换到 **Godot 教练**。
5. 切换只影响新会话；已经在跑的会话保持原来的装配。

## 装完第一句说什么

```
我要学 Godot，先用六周路线给我建 GODOT_PLAN.md 和 PROGRESS.md，然后从 W1 第一步开始。
```

它会先读工作区的这两个文件；不存在就按 W1–W6 路线建好，再问你当前周次和上次卡点。

## 每周怎么走

| 周 | 目标 | 交付物 |
|---|---|---|
| W1 | 官方 2D 教程 Dodge the Creeps 全流程 | 能跑通的示例项目 |
| W2 | 关教程从空项目重建 + 3 个自己的改动 | 你自己的版本 |
| W3 | 状态机、血条、场景切换 | 一个小 demo |
| W4 | TileMap 关卡、存档、音频 | 一个小 demo |
| W5 | 限时复刻 Pong / Snake，不许看教程 | 复刻作品 |
| W6 | 垂直切片 + 导出 exe | 可执行文件 |

每周日让它做一次 code review，更新 `GODOT_PLAN.md` 的完成状态和下周计划。

## 文件说明

- `package.json` —— bundle 清单，`dsh.bundle.patch` 指向下面的补丁。
- `cordis.patch.yml` —— 预设声明本体。改教学规则（铁律、路线、输出格式）就改这里 `persona.config.prefix`，然后重装一次。
- `LICENSE` —— MIT。

## 兼容性

- **DSH**：在本机 DeepSeek Harness `0.2.1-rc.3` 上验证（2026-11-17）。预设依赖一组 DSH 内部包（persona、agent-instructions、time-context、tool-fs、tool-web、compaction 等），DSH 升级后若插件名或配置项变化，需要同步改 `cordis.patch.yml`。
- **Godot**：锁定 `4.7.x` + GDScript（当前稳定版 4.7.2）。3.x 教程不适用，`@onready` / `@export` / `await` 的写法全变了。
- **平台**：Windows 走 `tool-pwsh`，其它平台走 `tool-bash`，已在预设里按 `process.platform` 分流。
