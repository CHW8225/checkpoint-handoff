# 设计依据与边界

核验日期：2026-11-29。下列开源项目用于了解思路，本 skill 未安装或执行其脚本，也未复制其实现。

| 来源 | 可借鉴内容 | 适用边界 |
| --- | --- | --- |
| [SUNRNEHUI/session-handoff-skill](https://github.com/SUNRNEHUI/session-handoff-skill) | 将中断会话导出为可携带交接文件 | 侧重交接，不能据此证明8%触发或自动唤醒 |
| [506218109-oss/codex-quota-handoff](https://github.com/506218109-oss/codex-quota-handoff) | 按重置时间调度、避免重复恢复、明确续跑目标 | 主要为 macOS shell/launchd；权限参数与配置改动不适合直接照搬 |
| [myseeway/QuotaHandoff](https://github.com/myseeway/QuotaHandoff) | 低额度时暂停、核对任务再恢复 | macOS、多账号轮换；超出本 skill 的单账号等待范围 |
| [OpenAI App Server 文档](https://learn.chatgpt.com/docs/app-server) | account/rateLimits/read、rateLimitsByLimitId、usedPercent、windowDurationMins、resetsAt | 文档提供额度接口，不证明任意客户端都具有自动恢复功能 |

本环境已实际成功调用原生 get_usage_limits，读到短周期窗口为300分钟。运行时仍应重新获取，不能把这个历史观察硬编码成所有账号的固定窗口。


安装 skill 不会自行创建后台服务。额度检查发生在已加载 skill 的执行回合；自动恢复依赖可用且成功登记的当前聊天 heartbeat，以及客户端/主机调度条件。没有真实跨重置测试，不应宣称保证无人值守恢复。
