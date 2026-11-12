# TODOS

## 发布演示用容器镜像

- **What:** 把"内核 + 模拟设备 + 演示模式"打包成容器镜像，打标签时由 CI 构建并发布到 GitHub 容器镜像仓库。
- **Why:** 让发起人之外的人不装 Node.js 也能运行演示。
- **Pros:** 演示零安装。
- **Cons:** 多一条发布流水线要维护；目前没有任何外部用户需要它。
- **Context:** 原设计的分发计划里有这一项。/autoplan 的 CEO 评审按审阅意见 R3-14 推迟：第一版唯一确定的用户是发起人。起点是 `docs/designs/nl-device-control-kernel.md` 的 Distribution Plan 一节。
- **Effort estimate:** human S / CC S
- **Priority:** P3
- **Depends on / blocked by:** 里程碑 1 完成；出现第一个需要运行演示的外部使用者。

## ROS2 仿真适配器

- **What:** 新增一个连接 ROS2 仿真环境的适配器，复用 ROS-MCP 或 ROSA 等现成件。
- **Why:** 验证适配器接口可扩展；覆盖"能运动的设备"，这是 Pocket 系列验证不了的部分。
- **Pros:** 不需要硬件；写出的设备层代码能迁移到真实机器人。
- **Cons:** 需要搭建 ROS2 和仿真环境，工作量为 M 到 L。
- **Context:** 设计文档里程碑 4。如果两台 Pocket 的真机测试都不通过，这一项会提前并替代里程碑 2。
- **Effort estimate:** human L / CC M
- **Priority:** P2
- **Depends on / blocked by:** 里程碑 1 完成。

## 把内核暴露为 MCP 服务器

- **What:** 给内核加一个 MCP 前端，让任何支持 MCP 的大模型客户端都能调用设备能力。
- **Why:** 不必自己做对话和语音界面；与 ROS-MCP 形态一致。
- **Pros:** 接入面更广。
- **Cons:** 对话过程在第三方客户端里，日志和确认机制需要重新设计才能保持同样的保证。
- **Context:** office hours 中的方案 C，当时未选，约定可在将来作为内核的另一个前端加入。
- **Effort estimate:** human M / CC S
- **Priority:** P3
- **Depends on / blocked by:** 里程碑 1 完成。

## 编写适配器的指南和骨架适配器

- **What:** 一篇"如何写一个适配器"的文档，加一个骨架适配器和一套适配器一致性测试。
- **Why:** 平台设想依赖适配器接口；目前接口只以代码中的类型定义和 Pocket 这一个范例存在。
- **Pros:** 第二个适配器的作者不必读内核源码。
- **Cons:** 接口在遇到第二种设备之前很可能要改，现在写指南容易作废。
- **Context:** /autoplan 开发者体验评审 D3。等第二个适配器（ROS2 仿真或行业无人机）做完、接口稳定后再写。
- **Effort estimate:** human M / CC S
- **Priority:** P3
- **Depends on / blocked by:** 第二个适配器完成。

## 按设备分别覆盖能力清单

- **What:** 允许在配置里为 Pocket 3 和 Pocket 4 分别覆盖能力清单，取代两台设备取交集的做法。
- **Why:** 某个动作只在其中一台设备上可用时，取交集会让它用不了。
- **Pros:** 不浪费单台设备的能力。
- **Cons:** 增加机型选择和两份清单的维护。
- **Context:** /autoplan 开发者体验评审 X4。CEO 阶段决定第一版取交集、不提供机型选择参数；两台设备的真机测试结果出来后再看是否有必要。
- **Effort estimate:** human S / CC S
- **Priority:** P3
- **Depends on / blocked by:** 两台设备的真机测试结果。

## 日志清理命令

- **What:** 一个手动的 `nlctl log prune`，清理旧日志和不再被引用的清单快照。
- **Why:** 日志和清单快照只增不减。
- **Pros:** 磁盘占用可控；减少本地保存的用户原话。
- **Cons:** 清理后旧条目无法重放。
- **Context:** /autoplan 开发者体验评审 X5。第一版明确不做自动清理。
- **Effort estimate:** human S / CC S
- **Priority:** P3
- **Depends on / blocked by:** 里程碑 1 完成。
