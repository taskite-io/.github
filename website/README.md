# Taskite 单页官网

原生 HTML、CSS、JavaScript，无构建步骤、包依赖、外部字体或第三方运行时。官网与 Console 独立。

## 本地预览

在仓库根目录运行：

```bash
python3 -m http.server 4175 --directory website
```

访问 <http://localhost:4175>。直接打开 `index.html` 也能阅读页面；复制按钮在安全上下文中使用 Clipboard API，不可用时会选中代码。

## GitHub Pages 发布

1. 在仓库 **Settings → Pages → Build and deployment → Source** 选择 **GitHub Actions**。
2. 将 `website/` 与 `.github/workflows/pages.yml` 合入 `main`，或在 Actions 手动运行 **Publish Taskite website**。
3. 工作流只上传 `website/`，由 GitHub 官方 Pages Actions 发布。默认项目地址是 <https://taskite-io.github.io/taskite/>，以工作流输出的 `page_url` 为准。

所有本地资源采用相对路径，支持项目子路径发布。未设置 `CNAME`；若使用品牌域名 `taskite.io`，在 Pages 设置里配置 Custom domain 并完成 DNS 配置，再启用 HTTPS。

## 页面与交互

- 品牌轨道首屏：PD 在上、Gateway 在左、Broker 在右、Worker 在下，四个组件嵌入 Taskite 的环形轮廓，中央使用平面的原始标识与字标。四个组件统一采用单个圆形图标，不以局部叠影区分副本能力；名称和职责分别位于图标的上下左右外侧，轨道不穿过文字，不需要文字背景遮挡。手机端收窄轨道，为左右标签保留空间。分段轨道与缓慢流光表达持续协作，具体通信关系由下方架构演示展示。
- 整体产品风格：可靠性图形、普通任务与生命周期复用圆角任务记录、圆形状态节点和蓝 / 青绿配色；页尾呼应同一品牌标识。手机端组件标签独立排版，不随 SVG 图幅缩小；正文、说明、代码与页脚保持可读字号。完整设计判断与 Apple HIG 依据见 [DESIGN.md](DESIGN.md)。
- 核心可靠性：复制日志、持久化状态、独立 Raft 分区、快照、租约与 Incident。
- 架构演示：任务流转、故障恢复、水平扩展。进入视口与切换 Tab 时自动播放，每轮结束后从头循环；可以暂停或逐步浏览。页面隐藏或离开视口时停止，重新进入时继续；手动暂停会保留。系统减少动态效果模式采用逐步浏览。
- 「持久化与复制」阶段与 Workite 对齐：突出承接任务的 Broker 3，以及三个 Broker 中的 P3 副本，保留其他分区的常态角色配色；后续完成上报继续落在 Broker 3 / P3。
- 上层是接入与路由，下层是控制面与数据面。Worker 与 Client 同尺寸，PD 与 Broker 集群框同高；折线采用圆弧转角和加粗箭头。Worker 只与 Gateway（Job API）及 PD（注册 / 续约）连接；Broker 节点与扩容节点位于虚线集群框内。连线根据组件的实际位置计算。
- 架构图布局与展示参照 Workite 官网：图幅至少 46rem 高，给节点和协调连线更多留白；常态 Leader 采用金色，高亮步骤采用蓝色节点和亮青色连线，故障采用红色。主连线 3.4px，复制连线 3px，分区和角色文字 12px；窄屏在图上方显示横向浏览提示，步骤说明采用更易读的字号。
- 生命周期：创建、就绪、激活、完成，并解释失败路径。
- 普通任务、ISO8601 与六字段 cron 定时器、多租户路由。
- Prometheus / Grafana 接入、构建 / Go SDK / tkctl 示例、开发状态与许可。

三副本只作示例。Leader 故障步骤保留可见的短暂不可用阶段；恢复以 Raft 多数派为前提。新 Broker 先以 Learner 追赶，再由变更计划晋升。未声明 exactly-once、可用性 SLA 或未经验证的性能数字。

## 设计依据

使用 `apple-design` skill 的 Web 适用原则：可访问性、层级、布局、品牌表达与目的明确的动画；不套用原生 macOS 导航规则。

参考：

- `references/hig/layout.md › Visual hierarchy`：按重要性安排内容，通过对齐与分组表达层级。
- `references/hig/branding.md › Best practices`：“Ensure branding always defers to content.” 品牌蓝集中用于主行动、轨道与选中状态。
- `references/hig/icons.md › Best practices`：“Maintain visual consistency across all interface icons…” 图标统一尺寸、描边、细节量与视角，并搭配组件名称及职责。
- `references/hig/design-principles.md › Simplicity`：“Be clear and direct.” 保留品牌轨道构图，通过图标和职责标签解释组件；完整通信关系集中在架构演示中。
- `references/hig/motion.md › Best practices`：“Make motion optional.” 全局暂停、逐步操作与系统减少动态效果模式均可使用。
- `references/hig/accessibility.md › Vision`：正文对比度、非颜色状态标签与键盘操作。

设计主张：品牌标识保留原有三节点形态，首屏将四个组件融入共同的产品轨道，解释“分布式任务，可靠执行”。轨道只表达组件协作，完整通信关系由架构演示说明。详细协议和存储实现交由文档；动态表达集中在任务流动、复制和角色变化上，可观测性使用示意图。

### Tokens

| 角色 | 浅色 | 深色 | 正文对比度目标 |
| --- | --- | --- | --- |
| 背景 | `#FFFFFF` | `#0E1726` | — |
| 次级背景 | `#F5F7FB` | `#131F32` | — |
| 正文 | `#111827` | `#EDF2FF` | 浅色 17.74:1 / 深色 16.03:1 |
| 次级正文 | `#596579` | `#A5B3CD` | 浅色 5.90:1 / 深色 8.49:1 |
| 链接 / 选中 | `#4657E8` | `#91A5FF` | 浅色 5.54:1 / 深色 7.72:1 |
| 状态 | `#087C7B` | `#60D7CB` | 浅色 5.02:1 / 深色 10.33:1 |

以上实测对比度分别以 `#FFFFFF` 与 `#0E1726` 为背景。按钮使用品牌 `#4657E8` 配白字，对比度 5.54:1。品牌图形保留 `#4657E8`、`#3478ED`、`#10AAA9` 原始颜色；图形颜色不作为正文色。

显示与正文使用系统无衬线字体；数据与代码使用 SF Mono / Consolas / Menlo。正文约 15–16px，首屏 48–70px，章节标题 32–48px，工具文字采用较小字号但保留足够对比度。导航与交互按钮至少 44px 高。SVG 架构标签在窄屏保留图幅并水平滚动，下方文字解释始终随页面宽度排版。

```text
Regular                           Compact
Logo    导航    动效 / GitHub        Logo          动效 / GitHub
                                  系统架构 / 可靠执行 / 开发者
首屏文案          品牌轨道           首屏文案 / 品牌轨道
三项可靠性                        三项可靠性（纵排）
完整架构 + 场景与步骤                架构（横向浏览）+ 场景与步骤
生命周期文案      状态演示           生命周期文案 / 状态演示
普通任务          定时任务           普通任务 / 定时任务
可观测性图        说明              可观测性说明 / 图
开发者文案        代码示例           开发者文案 / 代码示例
社区 / 许可                        社区 / 许可
```

深色模式跟随系统，支持 `prefers-reduced-motion`、`prefers-reduced-transparency` 和 `prefers-contrast`。Tab 组件支持左右箭头、Home / End；按钮有键盘焦点；架构角色同时使用文字和颜色。常态 Leader 用金色标识，当前步骤采用亮青色连线、明显的蓝色节点底色与边框，故障节点采用红色与“离线”文字。

箭头头部独立于线身绘制，线身止于三角形底部；箭头尖端与组件边框保留 6px 间距，避免圆端突出、动画线穿过尖端或箭头压住框线。自动循环时关闭连续的屏幕阅读器播报，手动逐步浏览时恢复状态播报。

架构图中「注册 / 续约」和「Leader 路由」标签与竖向连线保持 24px 水平间距，避免文字紧贴连线。

## 内容来源

- `.github/profile/README.zh-CN.md`：公开产品介绍与架构定位。
- `taskite/taskite-brand.md`、`taskite/taskite-icon.svg`：品牌定位、Slogan、色彩与原始标识路径。
- `README.zh-CN.md`、`docs/assets/taskite-technical-architecture.zh-CN.svg`：Gateway / PD / Broker 关系与 Multi-Raft。
- `placement-driver/README.md`：注册、拓扑变更、Learner-first、订阅 ACK 与补分发。
- `gateway-protocol/src/main/resources/gateway.proto`：以当前协议为准的 timer、生命周期、重试和 Incident 语义。
- `clients/go/README.md`：当前 SDK、CLI、Worker 会话与接入前提。
- `monitor/README.md`、`monitor/grafana/`：真实指标与现有 Grafana 看板。
- `LICENSE`、`COMMERCIAL-LICENSING.md`：源码可用许可与商业用途约束。

维护内容时优先核对当前协议。产品架构演示是说明性模型，修改协议不会自动修改页面。
