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
