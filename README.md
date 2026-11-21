# QQ AI 聊天搭子

一个跑在 QQ 群里的 AI 聊天搭子。基于 [AstrBot](https://github.com/AstrBotDevs/AstrBot) 框架，通过 OneBot 11 协议接入 QQ，对话模型使用 DeepSeek。

> **项目定位**：个人自用、小范围好友群内的聊天机器人，非商业用途，不对外提供服务。

## 这是什么

群聊里那种「爱冒泡的普通网友」——会接梗、会吐槽、不会突然开始给你上价值。目标不是当助手，是当搭子。

核心不是技术多复杂，而是**人设和说话方式调得像人**：

- 短句、口语、有毛边
- 不主动科普、不列清单、不劝人上进
- 大部分时候潜水，被 cue 到才说话

## 功能

| 功能 | 说明 |
|---|---|
| 自然群聊 | 支持上下文记忆，能接住前面的话题 |
| 稳定人设 | 提示词外置在 `persona.md`，改文件即改性格 |
| 触发控制 | @ 触发 / 关键词 / 随机参与，三种可组合 |
| 防刷屏 | 随机延迟、会话冷却、每小时回复上限 |
| 分段发送 | 长回复自动拆成多条，模仿真人打字节奏 |
| 内容过滤 | 可配置屏蔽词，命中直接忽略 |

## 架构

```
QQ 群
  ↕
OneBot 协议端（LLBot / Lagrange 等）
  ↕  OneBot 11 反向 WebSocket
AstrBot（人设 · 记忆 · 回复策略）
  ↕  HTTPS
DeepSeek API
```

AstrBot 负责逻辑，协议端负责登录 QQ 并做协议转换，两者通过 OneBot 11 标准通信——所以协议端可以随时替换。

## 目录结构

```
qq-ai-bot/
├── README.md              # 本文件
├── persona.md             # 人设提示词（核心文件）
├── config.example.yaml    # 配置模板，复制为 config.yaml 后填写
├── .gitignore             # 忽略密钥与运行时数据
└── LICENSE                # MIT
```

## 快速开始

1. **装 AstrBot**：参考 [官方文档](https://docs.astrbot.app/)，桌面客户端或 Docker 均可
2. **装协议端**：任选一个支持 OneBot 11 反向 WebSocket 的实现
3. **配置连接**：
   - AstrBot 侧新建 `OneBot v11` 机器人，反向 WS 端口 `6199`
   - 协议端侧新建 WebSocket 客户端，URL 填 `ws://127.0.0.1:6199/ws`
   - 日志出现 `aiocqhttp(OneBot v11) 适配器已连接` 即成功
4. **接模型**：填入 DeepSeek API Key，模型选 `deepseek-chat`
5. **装人设**：把 `persona.md` 的内容贴进 AstrBot 的人格 / 系统提示词
6. **调策略**：参照 `config.example.yaml` 设置触发方式、延迟和冷却
7. **测试**：用小号建测试群，@ 一下看反应

## 配置说明

`config.example.yaml` 里每一项都有注释。几个关键参数：

| 参数 | 建议值 | 作用 |
|---|---|---|
| `reply.trigger` | `["at", "keyword"]` | 别用纯随机，容易刷屏 |
| `reply.delay_seconds` | `[2, 8]` | 秒回不像真人 |
| `reply.cooldown_seconds` | `30` | 同一群冷却 |
| `reply.max_replies_per_hour` | `40` | 全局保险丝 |
| `persona.max_context_turns` | `12` | 上下文太长又贵又慢 |

## 注意事项

- **必须用小号**，主号不要碰
- 协议端和 AstrBot 装同一台机器，相关端口不要暴露公网
- 仓库里**不要提交** API Key、QQ 账号密码、登录凭证
- `config.yaml` 已被 `.gitignore` 忽略，只提交 `config.example.yaml`

## 免责声明

本项目仅供个人学习与自娱，使用者需自行承担因使用非官方协议接入 QQ 所带来的一切风险（包括但不限于账号封禁），并遵守相关平台的服务条款。作者不对任何滥用行为负责。

## License

[MIT](LICENSE)
