# Office Hours 记录：设备接入平台与自然语言控制插件

- 日期：2026-11-12
- 模式：公司内部项目（严格诊断）
- 阶段：只有想法，尚无产品和用户
- 出资人 / 拍板人：仓库所有者本人
- 状态：已完成。设计文档经 /autoplan 评审后由发起人按现状批准（2026-11-12）

本文件是 `/office-hours` 会话的结构化记录，随会话推进分批更新，不逐回合记录。

## 1. 想法（原话整理）

公司目前没有一个统一、快捷的软件平台，能够：

- 接入大疆设备；
- 接入常用的基于 ROS2 的机器人；
- 支持各种 CV 模型和大模型 API 的接入。

目标是从想法直接做到落地交付，全流程。希望基于平台开发插件，推广给公司内部或商用，并最好能产生专利。

## 2. 问答记录

| 编号 | 问题 | 回答要点 | 评估 |
|---|---|---|---|
| Q1 | 要做什么、给谁用、到哪一步 | 统一设备接入平台；只有想法；本人出资并拍板 | 是平台愿景，不是具体问题 |
| Q2 | 最近谁因为没有这个平台吃了亏 | 未给出具体的人或事；动机是自己有设备、想开发插件、想推广、想出专利 | 目前没有外部需求证据 |
| Q3 | 手上有哪些设备，最想做到的第一件事 | 第一个插件：自然语言（文字或语音）操控无人机等大疆产品。所在地区限飞，想先在 Osmo Pocket 4 上验证 | 插件方向明确；验证载体有问题，见第 4 节 |
| Q4 | 除 Pocket 外还有哪些设备和遥控器型号 | 未列出其他设备；表示 Pocket 3 也可以 | 暂按"手上只有 Pocket 系列"处理，待确认 |

## 3. 诊断结论（截至目前）

1. **没有人在等这个平台。** 现有理由都是发起人自己想要的，没有别人的痛点。这属于"先有方案，再找问题"。不代表不该做，但顺序要反过来：先做出一个具体有用的功能，再从中长出平台。
2. **平台不是产品，插件才是。** 第一个插件做什么，比平台架构重要。先做平台再找插件，容易做出没人用的空壳。
3. **范围过大。** 大疆 SDK、ROS2、CV、大模型四件事叠在一起，每件单独做都是数月工程。大疆的 SDK 体系与 ROS2 是两套东西。
4. **专利来自具体技术方案。** "统一平台"概念本身难以授权；可专利的点通常出现在解决具体难题的办法里。是否可授权需由专利代理人检索确认。

## 4. 已核实的事实：Osmo Pocket 能否作为验证载体

证据类型：网络检索（2026-11-12），未做真机测试。

- Osmo Pocket 4 / 4P 不在 DJI Mobile SDK 的支持列表中，未找到任何官方开发者文档说明它被某个 SDK 支持。
- Pocket 4 的控制只能通过官方 DJI Mimo 应用（蓝牙和 Wi-Fi）。
- Pocket 3 有两个开源逆向项目：
  - node-osmo：通过蓝牙配对、连 Wi-Fi、设置分辨率 / 帧率 / 码率 / 防抖模式。
  - djictl：替代 Mimo 实现直播推流。
- 上述项目都没有实现直接控制云台转动。另一篇逆向 Osmo 云台蓝牙协议的记录中，作者未能弄清如何驱动云台电机。

对验证方案的影响：

| 方案 | 能验证什么 | 不能验证什么 | 风险 |
|---|---|---|---|
| Pocket 3 + 逆向协议 | 自然语言 → 相机参数、录制 / 直播类指令；视频流接入 CV 和大模型 | 云台运动控制；任何可迁移到无人机的设备层代码 | 协议非官方，固件更新可能失效；不能作为商用基础 |
| Pocket 仅作 USB 摄像头 | 视觉识别、大模型看图 | 任何设备控制 | 低 |
| DJI SDK 自带模拟器 | 自然语言 → 真实飞行指令与回传，无需起飞 | 真实飞行环境 | 需要 SDK 支持的机型；限飞区内能否启用未核实 |
| ROS2 + Gazebo 纯软件仿真 | 自然语言 → 指令的完整链路；ROS2 侧接入 | 大疆侧接入 | 不需要任何硬件 |

当前判断：真正需要验证的是"一句话能否稳定、安全地变成正确的设备指令"这一段，它不依赖真机起飞。Pocket 3 可以做一个演示，但不应作为平台的起点。发起人倾向先用 Pocket 验证，最终由发起人决定。

## 4a. 行业现状检索

证据类型：网络检索（2026-11-12），每个主题一次检索，只读了摘要，未逐一核实仓库代码。

| 方向 | 已有成果 | 对本项目的含义 |
|---|---|---|
| 自然语言控制无人机 | PX4 + ROS2 + 本地模型的开源智能体（arXiv 2506.07509，代码 limshoonkit/ros2-agent-ws）：指令格式全部合法，但任务成功率最高只有 40%。另有面向 ArduPilot 的 FLUC、基于 AirSim 的研究系统 | 已是公开的研究方向，概念本身不新。难点在可靠性，不在"能不能做" |
| 大模型 + ROS2 | ROSA（NASA JPL）、RAI（RobotecAI）两个智能体框架；ROS-MCP 服务器把话题、服务、动作暴露给大模型 | ROS2 侧的"接入大模型"已有现成轮子，不应重做 |
| 大疆 + ROS2 | psdk_ros2（umdlife）：把大疆 Payload SDK 包装成 ROS2 接口，支持位置 / 速度控制、云台与相机管理、视频流；Aerostack2 基于它支持 M30、M300 RTK、M350 RTK | 大疆行业机型接入 ROS2 已有开源方案，但只限支持 Payload SDK 的行业机，不含 Pocket 和消费级机型 |
| 常见错误 | 把大模型当作安全层；给智能体暴露过多工具；缺少错误回馈；小模型与大模型表现差异大；把研发框架当成可量产产品 | 2026 年 ROSClaw 论文：相同约束下不同模型的越界动作提议率相差可达 4.8 倍，执行层设计比提示词更影响安全 |

三层归纳：

- **公认的做法**：大模型负责理解和规划，通过一组受限的工具调用设备；ROS2 是机器人侧事实标准。
- **当前的讨论**：瓶颈是可靠性和安全。开源框架都自称研发用途，不是量产级。
- **对本项目的判断**：常规做法是对的，应当建立在它之上。"统一接入"这一层已经是现成的开源件，不构成差异。可能的差异点在两处：一是大模型与设备之间可验证的指令校验与执行层；二是现有方案没有覆盖的大疆设备。专利若要有，更可能出在第一处。

## 4b. 前提确认

| # | 前提 | 结果 |
|---|---|---|
| 1 | 先做插件，不先做平台 | 同意 |
| 2 | 复用开源件，自己只写中间的翻译与校验层和缺失的设备适配 | 同意 |
| 3 | 大模型不直接驱动设备，指令必须经过独立校验层 | 同意 |
| 4 | 验证以 ROS2 仿真为主，Pocket 仅作演示 | **否决。改为以 Osmo Pocket 3 为第一验证载体**（发起人决定） |
| 5 | 先内部用，商用推迟 | 同意 |

关于第 4 条的保留意见：Pocket 3 依赖非官方逆向协议，没有云台控制，设备层代码不能迁移到无人机，不能作为商用基础。对策是把设备层做成可替换的适配器。

## 4c. 方案对比与选择

| 方案 | 概要 | 工作量（人工 / CC） | 结果 |
|---|---|---|---|
| A. Pocket 3 直连演示 | 命令行程序把大模型输出直接接到 node-osmo | 约 1 周 / 约半天 | 未选：换设备等于重写，没有不依赖硬件的测试 |
| B. 指令内核 + 适配器 | 前端、内核（翻译、校验、日志）、设备适配器三层；第一版带 Pocket 3 和模拟设备两个适配器 | 约 3 到 4 周 / 约 2 到 3 天 | **已选** |
| C. MCP 服务器 | 把设备能力包装成标准工具，由第三方大模型客户端充当入口 | 约 1 周 / 约半天 | 未选：依赖第三方客户端，难以独立交付 |

第二意见（独立 AI 冷读复核）：发起人选择跳过。

## 4d. node-osmo 能力核实（更正第 4 节）

依据：node-osmo 仓库 README（2026-11-12 读取），未做真机测试。

- 有文档依据：配对、连接 Wi-Fi、设置直播的分辨率 / 帧率 / 码率 / 防抖模式、向指定 RTMP 服务器开始直播、读取电量。
- README 未提及，待真机核实：停止直播、本地录像、读回设备状态、云台转动。
- 没有命令行工具，只有示例代码；基于 noble 蓝牙库，各操作系统行为不同；作者自述尚未完全稳定；MIT 许可。

更正：第 4 节曾写"相机参数、录制 / 直播类指令"，其中录制没有依据，实际有依据的是直播相关指令和电量读取。

## 4e. Osmo Pocket 4 能否作为验证载体（更正第 4 节）

依据：扩大范围的网络检索和各项目主页（2026-11-12），未读代码，未做真机测试。

第 4 节曾写 Pocket 4 没有公开控制方案。更正：官方 SDK 确实没有，但社区已有逆向成果。

| 项目 | 对 Pocket 4 的说法 | 形态 |
|---|---|---|
| Osmosis（KonradIT） | Pocket 3、Pocket 4 / 4 Pro 均列为"已在真机验证"；`MEDIA_PROTOCOL.md` 记录了通过蓝牙开始和停止 RTMP 直播的指令 | 安卓应用，MIT |
| dji-remote（dimadesu） | 标为支持，但作者注明更新后未确认可用 | 安卓应用 |
| node-osmo（datagutt） | 只列 Action 3/4/5 和 Pocket 3 | TypeScript 库 |
| osmo-live（KevinCowleys） | 列了 Pocket 3，未列 Pocket 4；只在 Action 4 上测试过 | Go 命令行工具 |

结论：Pocket 4 可以试，但不能假定可用。我们计划调用的 node-osmo 没有声明支持它。处理方式是把 Pocket 4 作为同一个适配器下的未核实机型，真机测试时两台都测；node-osmo 连不上时，再评估按 Osmosis 协议文档补差异的工作量。能力范围与 Pocket 3 相同，限于直播相关指令，没有录像控制、云台转动或状态读回的记录。

另一处更正：第 4 节把直播能力只归于 djictl。实际上 node-osmo 的 README 也列有"向指定 RTMP 服务器开始直播"，本设计只使用 node-osmo。

## 4f. 开发工具分工

- gstack 负责流程两端：`/office-hours`（本次）、`/autoplan` 评审、`/review`、`/ship`。
- pstack 负责中间的编码过程：`/pstack:poteto-mode` 为主工作模式，配合 `/pstack:architect`、`/pstack:tdd`、`/pstack:statechart`（校验规则本质是状态机）、`/pstack:typescript-best-practices`。
- 开发前需先运行一次 `/pstack:setup-pstack`，把 pstack 的模型角色映射到 Claude Code 中可用的模型。

## 4g. 设计文档审阅进展

| 轮次 | 评分 | 必修问题 | 处理 |
|---|---|---|---|
| 第 1 轮 | 6/10 | 11 个 | 全部修改，第 2 轮确认已解决 |
| 第 2 轮 | 7/10 | 4 个（状态无法从"未知"恢复、第一版无法停止直播、确认提示的时机、用户原话中的密码未屏蔽） | 全部修改，第 3 轮确认已解决 |
| 第 3 轮 | 7/10 | 5 个（均由加入 Pocket 4 和连接后询问直播状态引出：两处说法矛盾、里程碑 2 未区分两台设备、"连接"后带后续步骤无法预先校验、已连接时再"连接"未定义、询问的可接受回答未定义） | 已修改。审阅上限为 3 轮，这 5 处修改已写入文档但没有再经审阅者确认 |

三轮合计：确认解决 15 个必修问题；另有 19 个次要问题记录在设计文档末尾的 "Reviewer Concerns" 一节，未修改。

设计文档：`docs/designs/nl-device-control-kernel.md`（状态：APPROVED）。

## 4h. /autoplan 评审与最终决定

2026-11-12 对设计文档运行了 `/autoplan`（CEO、开发者体验、工程三个阶段；设计阶段因无界面跳过；Codex 未安装，没有外部模型视角）。

- **最重要的事实修正**：node-osmo 对外只有一个组合调用 `startLiveStream(Wi-Fi 名称, 密码, 推流地址, 分辨率, 帧率, 码率, 防抖)`，创建设备对象时必须给出设备标识和机型。原设计的"连接、连 Wi-Fi、设置参数、开始直播"四个独立设备动作不存在。设计改为"暂存动作加一个设备动作"，并把读库源码定为开工前的第一项工作。
- **各阶段**：CEO 范围复核三轮通过（6.5、7.5、8.5）；开发者体验计划评分由 4 升到 7；工程评审 51 个发现，0 个关键缺口。
- **发起人在最终关口回复 `as-is`**：按计划现状批准。未采纳的三项建议：把 ROS2 仿真适配器加入里程碑 1；以具名用户作为里程碑 2 的前提；精简里程碑 1。专利目标保持现状，不做安排。
- **文档**：`docs/designs/nl-device-control-kernel.md` 已重写为合并后的定稿；评审全过程存档在 `docs/designs/archive/2026-11-12-nl-device-control-kernel-review-record.md`；推迟的工作在 `TODOS.md`。
- **pstack**：所有角色的模型设为 Opus（xhigh）。

## 5. 未决问题

- [ ] 手上是否还有 SDK 支持的无人机或其他机器人（型号、遥控器型号）。
- [ ] 第一个版本做成后，第一个给谁看（公司内部哪个部门，或哪个外部客户）。
- [ ] 限飞区内 DJI SDK 模拟器能否启用（需真机测试）。
- [ ] Pocket 3 的云台控制协议是否已有可用的公开实现。

## 6. 下一步

- ~~行业现状检索~~（已完成，见 4a）。
- ~~前提确认~~（已完成，见 4b）。
- ~~备选方案对比与选择~~（已完成，见 4c）。
- 正式设计文档：`docs/designs/nl-device-control-kernel.md`，三轮审阅完成，发起人已批准。
- 发起人作业：写下第一个内部用户的名字；在本地电脑上跑一次 Pocket 3 真机测试并记录结果。

## 参考来源

- Osmosis：https://github.com/KonradIT/Osmosis
- dji-remote：https://github.com/dimadesu/dji-remote
- osmo-live：https://github.com/KevinCowleys/osmo-live
- node-osmo 仓库：https://github.com/datagutt/node-osmo

- DJI Mobile SDK for Android 4 支持产品列表：https://sdk-forum.dji.net/hc/en-us/articles/25516653720601
- DJI Mobile SDK V4 下载页：https://developer.dji.com/mobile-sdk-v4/downloads/
- Pocket 4 连接手机说明（heliguy）：https://www.heliguy.com/blogs/knowledge-base/how-do-i-connect-osmo-pocket-4-to-my-phone/
- Pocket 4 / 4P 固件汇总（Newsshooter）：https://www.newsshooter.com/2026/10/05/dji-osmo-pocket-4-and-4p-firmware-roundup/
- node-osmo：https://github.imc.re/datagutt/node-osmo
- djictl（xaionaro/reverse-engineering-dji）：https://backiee.wasmer.app/http_github_com/xaionaro/reverse-engineering-dji
- PX4 自然语言控制智能体（arXiv 2506.07509）：https://arxiv.org/abs/2506.07509v1
- ROSA（arXiv 2410.06472）：https://arxiv.org/pdf/2410.06472
- RAI，ROSCon 2024 演讲：https://roscon.ros.org/2024/talks/Agent-based_AI_Framework_for_ROS_2.pdf
- ROSClaw（arXiv 2603.26997）：https://arxiv.org/html/2603.26997v1
- Open Robotics 论坛：机器人智能体的经验与实践：https://discourse.openrobotics.org/t/building-and-integrating-ai-agents-for-robotics-experiences-tools-and-best-practices/52465
- psdk_ros2：https://github.com/umdlife/psdk_ros2
- psdk_ros2 发布说明：https://discourse.openrobotics.org/t/introducing-psdk-ros2-bridging-djis-psdk-libraries-with-ros-2/33500
- Aerostack2 的大疆 PSDK 平台包：https://index.ros.org/p/as2_platform_dji_psdk/
- 逆向 DJI Osmo 云台蓝牙协议的记录：https://aaadityaart.notion.site/Reverse-Engineering-the-DJI-Osmo-37b18cae2ab08098acefd85694a070bd
