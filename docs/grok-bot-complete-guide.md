# Grok Bot 完全教程（中文 · 无来源版）

> 整理日期：2026-10-20（北京时间）｜面向：中文用户（新手到进阶）
> 本版在完整资料汇编基础上**去掉了外链与出处标注**，保留全部操作步骤、功能说明与注意事项。产品更新很快，请以应用内实际界面为准。菜单名称保留英文原文（如 **Connect apps**、**Test run**），方便对照。文中「未证实：…」表示尚未被官方文档明确写出的说法，仅供参考。

---

## 目录
1. 产品概览与核心概念
2. 入门设置（套餐、安装、登录、第一个 Bot）
3. 连接器 / 插件（Connect apps、MCP、X、邮箱、1Password）
4. 例程（Routines）
5. 技能（Skills）与 Teach a task
6. 云电脑与浏览器
7. 语音与渠道（语音、移动端、Slack、 on X、邮件）
8. 多 Bot / 群聊 / Team Bots（团队）
9. 写代码与云端代理（Cursor Cloud Agents）
10. 高阶技巧（社区最佳实践）
11. 视频教程步骤汇总
12. 常见问题与坑
13. 社区资源索引（本版从略）
附录 A：版本时间线｜附录 B：说法差异提醒｜附录 C：资料缺口说明

---

## 1. 产品概览与核心概念

### 1.1 Grok Bot 是什么
- **定义**：Grok Bot 是 SpaceXAI（原 xAI + Cursor 团队）推出的"AI 队友"产品。官方介绍：Bots 是能替你做真实工作的 AI 队友，会登录你的工具、像你一样使用它们，然后把做完的结果带回来。2026-08-11 以 early beta 发布。（2026-08-11）
- **它和 Grok 聊天不一样**：Grok 是你问它答的聊天助手；Grok Bot 是你派活、它在云端电脑上自己干完。Grok Build 则是写代码的 CLI / coding agent，三者是不同产品。 ；
- **平台**：macOS、Windows、Linux 桌面版，以及 iPhone（iOS 18+，也能在 iPadOS 18+ 上用）和 Android（9+）。、；Android 版 2026-09-02 上线

### 1.2 核心概念速查表
| 概念 | 一句话说明 |
| --- | --- |
| **Bot** | 一个长期存在的 AI 队友：有名字、有一项工作、有自己的对话，工作上下文会随时间积累 |
| **共享云电脑** | 一个用户只有一台持续运行的云电脑，**所有 Bot 共用**：浏览器 cookie/登录、文件、命令行凭据都共享。每个 Bot 在这台电脑上有自己的屏幕（screen），可以并行操作，但**屏幕不是安全边界** |
| **/workspace** | 云电脑上的共享工作目录，Bot 之间可以通过它交接文件 |
| **连接器 / 插件** | 在 **Connect apps** 里安装的结构化集成（Gmail、Slack、Notion……），全账号通用，不单独属于哪一个 Bot |
| **Skill（技能）** | 可复用的"怎么做"说明；私有技能是一个库，所有 Bot 共享 |
| **Routine（例程）** | 告诉**某一个** Bot"什么时候做"：按时间表，或由事件触发（Slack、GitHub、Linear、Sentry、PagerDuty、邮件、webhook 等） |
| **群聊** | 2–6 个 Bot 放进同一个对话，交接过程大家都看得到 |
| **Auto-review** | 工具调用、电脑操作执行前由模型先审一遍；可以加 **Ask first** / **Allow automatically** 规则 |
| **Primary Bot（主 Bot）** | 名字旁有星标的 Bot； on X 的请求交给它；从 v0.66.0（2026-10-02）起它会**主动**发现能替你做的事并提议 |
| **Team Bot** | 一个人拥有、分享给 Cursor 团队的 Bot；每个人和它的对话互相看不到 |
| **模板（Template）** | 把一个 Bot 的配置（身份、说明、技能、例程）分享出去，别人添加后得到一份**副本** |

### 1.3 官方的三条设计理念（与社区共识）
1. **一个 Bot 一个岗位，不要做"万能助手"**："Focused Bots build more useful context than one catch-all Bot"；**General Helper** 这种岗位给 Bot 的指引太少。。官方提示清单里也有"bots as roles / think in roles, not tasks"。
2. **先跑通一次 → 存成 Skill → 再做成 Routine**："Start with a one-time task. Make it reliable, save the method as a skill, and only then automate it." 。官方设计师 Peng、工程师 Lauren 的访谈也是这个顺序。（2026-09-27）
3. **能力共享，记忆分开**：工具、连接器、技能所有 Bot 共用；记忆和偏好属于各自的 Bot（Peng 原话：intentional design）。"Bots have separate roles and conversations, but they share the computer"

### 1.4 模型
- Grok Bot **没有模型选择器**，由系统按任务挑后端模型，可能是 xAI 自家模型，也可能是 sub-processor 名单上的第三方模型（官方帮助举例 Opus 5.5），用户看不到某次请求用的是哪个模型，也不能指定或屏蔽；团队的 Cursor 模型白名单对 Grok Bot 不生效。官方说"路由到 Opus 5.5 不会改变你的每 token 成本"。
- 社区报道：Elon 在 10 月上旬宣布 Grok Bot 会按任务使用最佳后端模型（包括 Claude Opus、Midjourney、Suno 等）。有说法称现在"默认就是 Opus"，此前默认 Grok 4.7。（2026-10-12）⚠️ "默认 Opus"属于未证实说法，官方只说"按任务选择"。
- ⚠️ 有人在 prompt 里点名要求子代理用某个模型。对 **Cursor Cloud Agent** 可能有效（云代理可以按任务选模型/推理强度），但对 Bot 本身，官方明确说不能指定。

### 1.5 它适合干什么（官方/社区例子）
- 官方用例页按岗位分类（工程、PM、设计、销售/GTM、SDR、营销、效果广告、客服、售后、法务运营、招聘、移动开发……）。、
- 官方自己的案例：用 Grok Bot 重建客服（2026-09-22）；内部采购 Bot "Haggle Bot" 一周省下 10 万美元以上（2026-09-04，）；3 名员工直播 3 天用 Grok Bot 建一家公司（2026-09-15~17）
- 社区观点（多位作者一致）：它最强的不是帮你想复杂策略，而是**每天重复、又烦又固定的例行工作**。

### 1.6 和其他 Agent 怎么选（社区对比，仅供参考）
- 和 Hermes Agent / OpenClaw 比：那两个要自己用终端、Python、配置文件和服务器部署，开源免费但模型费自己付；Grok Bot 下载就能用，托管在云端，但要付费订阅。有人打了个比方："自己组装 PC vs 直接买 MacBook"。
- Grok Bot 是"唯一能免费读 X 数据的 personal agent"，可以把多个 Bot 放进同一个线程，这一点 ChatGPT dots / Muse 做不到。
- 社区还整理了 Hermes/OpenClaw 等的对比，以及自托管替代方案清单。、（"自托管与平替"一节）

---

## 2. 入门设置

### 2.1 套餐与资格（⚠️ 2026-10-12 起有重大变化）
**新用户（2026-10-12 之后）：The X Subscription**
- 一个订阅同时覆盖 Grok、Grok Bot、Cursor 和 X Premium。**Premium $30/月、Super $100/月、Ultra $200/月包含 Grok Bot**；**Free 和 Plus 不包含**。团队席位价格相同，可以在同一个团队里混用。（价格）、（2026-10-12 更新）
- Grok Bot 的用量和 Cursor、Grok 共用**同一个每周池子**；池子用完后从预付费 credit 里扣（可以开 Auto Top-up，并在 grok.com/dashboard/billing → **Additional usage credits** 设每月上限）。查用量：grok.com/dashboard → **Usage**。
- 统一账户（unify）是可选的，但**一旦完成就不能撤销**，客服也撤销不了；切换时旧的 Cursor/Grok 订阅会取消并按比例退款（App Store/Google Play 购买的要按各平台规则处理）。入口：cursor.com/unify。
- 新套餐里，第三方模型（比如 Cursor 里用的 Opus）按 provider 的 API 列表价从 usage credits 扣，而旧 Cursor 套餐对第三方模型有补贴。所以**重度使用第三方模型写代码的人可能更适合留在旧套餐**。

**老用户（旧套餐继续有效）**
- 旧 Cursor 方案：Pro、Pro+、Ultra 个人版和 Cursor Teams 都包含 Grok Bot，额度依次是 Pro < Pro+ < Ultra。也可以把**个人版** SuperGrok / SuperGrok Plus / SuperGrok Heavy / X Premium+ 链接到 Cursor 账户；或者用自助版 Grok Business 已分配的席位，通过 **Sign in with Grok** 登录。SuperGrok Lite 不包含。
- **额度不叠加**：Pro = SuperGrok = X Premium+ 同档，Pro+ = SuperGrok Plus 同档，Ultra = SuperGrok Heavy 同档；同时有两个时，取额度更高的那个。
- **SuperGrok 链接是永久的**：不能解绑，也不能换绑到别的 Cursor 账户，链接前一定要确认登录的是哪个 Cursor 账户。
- 官方没有公布每一档的具体额度（论坛员工 2026-10-20 的回答）。
- **免费试用**：是一笔"用量额度"而不是按天算（同时有 7 天窗口）；一个大任务就可能把它用光，用掉的不退。**Cancel Trial** 会立即清空剩余额度，而且之后不能再领。

**历史时间线（理解旧教程里的价格为什么对不上）**
- 2026-08-21：开放给 SuperGrok Plus、Cursor Pro+、Cursor Teams 订阅者，其他人可以领有限额度的免费试用。
- 2026-08-26：所有 SuperGrok 和 Cursor Pro 订阅者都能用。
- 2026-09-03：Enterprise 版上线。
- 2026-10-12：推出 The X Subscription。
- ⚠️ 所以 8 月的中文教程（例如 v260823 写的"最低 Cursor Pro+ $60"）、9 月的视频（"只支持个人版 SuperGrok，企业版不行"）都已经过时。

**对中国大陆和港澳用户的特别提醒（社区经验）**
- on X 只看 X 记录的账户所在国家，VPN 没用；澳大利亚、英国、欧盟、冰岛、列支敦士登、挪威暂不支持。
- 云电脑的出口 IP 位置不固定，有时会被判定在中国（上海、郑州），导致部分美国 API 返回 HTTP 451；目前没有固定地区的设置。（2026-10-14）。社区 认为长期用大陆 IP 操作会让 bot 电脑的出口被标成中国。（2026-10-14，个人经验）
- 有人建议装好桌面端后关掉"在这台电脑上执行"和"经此电脑转发流量"（建议）。（2026-10-18）
- 香港用户：有视频说"香港不用 VPN、港卡可以付款"（2026-09-24）；另有说法称官方支持地区不包括香港、账户有风控风险（2026-10-08）⚠️ 两种说法矛盾，请以 App 内实际情况为准。
- ⚠️ 有中文教程挂了人民币"代购月卡"链接（第三方转售），本文不推荐，账号风险自负。

### 2.2 安装桌面版（官方步骤）
- **macOS**：在下载页选 Apple silicon 或 Intel 版（**Apple menu → About This Mac**：显示 Chip 是 Apple silicon，显示 Processor 是 Intel）→ 打开 dmg → 把 **Grok Bot** 拖进 **Applications** → 打开，系统询问时选 **Open**。
- **Windows**：选 x64 或 Arm64（查看方法：**Settings → System → About → System type**）→ 运行安装程序 → 从开始菜单打开。
- **Linux**：在 x.ai/bot 的 **More downloads** 里下载，有 `.deb`（Debian/Ubuntu）、`.rpm`（Fedora/RHEL）和 AppImage。架构用 `uname -m` 查。
- 更新：自动检查，也可以在 **Settings → Updates → Check for Updates** 手动检查。版本落后超过 14 天、且已有新版时，会强制要求 **Update required**。Linux 更新命令：`sudo apt update && sudo apt install --only-upgrade grok-bot` 或 `sudo dnf upgrade --refresh grok-bot`。
（2026-10-16 更新）

### 2.3 登录与链接账户
1. 欢迎页选 **Sign in**（已经在 App 里的话，用 Settings 里的 **Sign In with Cursor**）→ 在浏览器完成认证 → 回到 App。
2. 如果额度来自 SuperGrok：手机上点 **Link Grok Account** → **Finished Linking? Refresh My Status**；桌面上在 **Settings → Usage & Billing → Get Access with Grok**。
3. 账号要求：Grok Bot 需要云端数据存储，用 **Legacy Privacy Mode** 的账户要先改设置才能开始用。
4. 第一次使用时会介绍 Bot、共享电脑和例程，并问你平时用哪些工具（这些回答只用来推荐 Bot，**不会自动连接或改动这些工具**），最后打开 **Meet a future teammate**。

- 移动端：**Log In or Sign Up** → 浏览器认证 → 回到 App。
- 一台桌面电脑上可以保存多个账户：账户菜单 → **Switch account** / **Add account**（v0.48.0，2026-09-09）。

### 2.4 创建第一个 Bot
**官方路径**
1. 在 **Meet a future teammate** 里选一个推荐队友，或者选 **Create your own**，给它：短名字 + 一项主要工作 + 一段"怎么工作"的描述。官方示例：Name: Piper / Job: Product performance / Description：用可观测性工具调查性能问题，保留链接和截图，把证据和假设分开，最影响的问题放最前面，**永远不要改生产设置**。
2. 之后新建：侧边栏 **New** 或 `Cmd/Ctrl+N` → **New chat** 里选 **Create new Bot**，或者输入名字后选 **Create "name" Bot**。
3. 什么时候该单独建一个 Bot：目标/负责领域、工具和信息源、工作风格、审批边界、周期性日程，只要有一项不同就分开建。

⚠️ **"描述（Description）"字段的变化**：官方 docs 写的是通过 **Edit Profile** 或 **View conversation details → Bot settings** 编辑 Name / Label / Description（docs 更新于 2026-10-12/17）。但帮助中心 edit-bot 页和论坛员工都说：**桌面端个人 Bot 的详情里已经没有 Bot settings 页和描述字段了**（现在只有 Details / Media / Computer，Details 里只显示 Routines），要改 Bot 做什么，直接在聊天里告诉它，长期规则就让它"记住"。Team Bot 的描述在 **About** 里。移动端还是旧布局。；（2026-09-30~10-12）、（2026-10-05，员工说这是有意的重新设计）。很多 8–9 月的视频（比如 有人演示过的"computer 图标 → Settings → Name/Description"）显示的是旧界面。

**改名、改头像（2026-10 现行界面）**：右键侧边栏里的 Bot → **Rename Bot**（或双击名字）；也可以点聊天顶部的 Bot 名字打开详情，再点名字/标签编辑。头像：详情里点头像 → **Bot**（角色外观）/ **Generate**（文字生成）/ **Upload**（小于 25 MB）→ **Set avatar**。手机上叫 **Edit name**，标签叫 **Title (optional)**。

### 2.5 第一个任务怎么写
官方的五要素：**Outcome**（要完成什么）/ **Sources**（用哪些应用、网站、文件）/ **Constraints**（不能做什么、做什么之前要先问）/ **Deliverable**（交付什么）/ **Review point**（什么时候停下来等你）。
- 官方推荐的"5 分钟首个任务"（不需要连接器）：附一个文档，然后说"Summarize this document in five bullets. List every date, decision, and open question in a separate section. Cite the page or section for each item. Do not change the source file."
- 中文模板（社区整理，对应官方五要素）：目标 / 来源 / 步骤 / 交付 / 限制 / 核对。（2026-10-12 核对）
- 粤语教程的"四行 brief"示例：①你是我的 AI 调查员 ②任务：过去 7 天三条 AI 视频工具的更新 ③每条要有标题、重点、链接、日期，用网页搜索 ④找不到或不确定就直接说，不要猜。效果明显比只说"帮我找最近的 AI 新闻"好。
- 输入方式：直接打字；**Start voice input**（`Cmd/Ctrl+D`）语音输入；**Start voice chat** 实时语音对话。
- 结果不满意时，把长期偏好说清楚，例如"Use this format for future weekly reports: five bullets, source links inline, and a final section called 'Decisions needed.'"；流程稳定后让它存成 skill 或建 routine。

### 2.6 "大脑倾倒 + 反向配置"（社区高赞入门法）
- 先把你的工作、工具、烦心事一股脑告诉主 Bot，然后让它反过来问你问题、建议该建哪些 Bot 和例程。；第 03 章
- 让 Bot 自己写自己的指令：新建 Bot 后用大白话 brain dump 它的职责，它会自己整理出指令。
- 一句话让"总管"按你的身份自动建一组 Bot（香港老师案例）。

### 2.7 侧边栏整理
- **Pin** 把常用 Bot 固定在顶部；**Hide from sidebar** 隐藏但不删除（隐藏后**不会暂停**它的例程，也不会再发通知）；侧边栏底部 **Hidden Bots** → **Show in sidebar** 恢复显示。
- **分组（Sections）**：桌面端右键 Bot → **Move to new section** / **Move to** → **Create section**，右键分组名 → **Rename**；iOS 上长按 → **New Section**（需要 v1.2.0+），分组在 iOS 和桌面之间同步；删除分组后里面的 Bot 会移到 **Unassigned**。
- **Duplicate**：复制 Bot 的资料、设置、已启用的技能、例程和头像，**不复制**对话历史、学到的记忆和附件，适合"同一个岗位、不同地区"这类场景。
- 删除 Bot 会删掉它的资料、对话和例程，但**共享电脑上的文件和登录状态不会删**；以后可能还用得上的话，建议隐藏而不是删除。
- 社区做法：按工作/生活分组，常用的 3 个置顶（Peng）；按角色给 Bot 配不同的颜色和形状

### 2.8 快捷键（桌面版，官方完整列表）
`Cmd/Ctrl+K` 搜索/跳转面板 · `Cmd/Ctrl+Shift+F` 搜索 Bot · `Cmd/Ctrl+N` 新 Bot/新对话 · `Cmd/Ctrl+F` 在当前对话中查找 · `Cmd/Ctrl+B` 收起侧边栏 · `Cmd/Ctrl+I` 聚焦输入框 · `Cmd/Ctrl+L` 把选中文本加入对话 · `Cmd/Ctrl+1…9` 切到侧边栏第 1–9 个 Bot · `Alt+↑/↓` 上一个/下一个 Bot · `Cmd/Ctrl+[ / ]` 后退/前进 · `Control+Tab` 循环切换 Bot · `Cmd/Ctrl+Shift+M` 或 `Cmd/Ctrl+Shift+W` 打开 Connect apps · `Cmd/Ctrl+Shift+,` Bot 设置 · `Cmd+Shift+I` / `Cmd+Alt+B`（Mac）、`Ctrl+Alt+B`（Win/Linux）对话详情 · `Enter` 发送、`Shift+Enter` 换行 · `Cmd/Ctrl+D` 语音输入 · `Cmd/Ctrl+,` 打开设置 · `F11` 全屏。语音对话没有快捷键。
（2026-10-17 更新）
- 其他：输入 `@` 引用 Bot/群/例程/连接器；`:` 选表情；选中聊天里的文字后按 `Cmd/Ctrl+L` 引用；在输入框输入 `-` 或 `1.` 开始列表。⚠️ docs 写"输入 `/` 引用技能"，但 changelog v0.68.1（2026-10-07）写的是"Typing / in the composer no longer opens a menu of skills and actions"，两者不一致；另一位官方工程师 Lauren 9 月的访谈里还在用 `/potato-mode` 调用技能。

### 2.9 界面语言
- 桌面版支持 36 种语言（包括简体中文、繁体中文），在 **Settings → General → Appearance → Language** 设置；手机版语言较少（12 种）。新建的 Bot 会用界面语言和你对话，除非你用别的语言跟它说话。语音对话有单独的 **Language** 设置。
- 系统设为粤语时，**Follow System** 会自动选对应的繁体或简体中文（v0.39.0）。

---

## 3. 连接器 / 插件（Connect apps）

### 3.1 官方添加步骤
1. 侧边栏打开 **Connect apps**（快捷键 `Cmd/Ctrl+Shift+M`），或者点聊天里出现的 **Connect** 卡片；手机上点左上头像 → **Connect apps**。
2. 浏览或搜索应用 → **Add**。
3. 出现 **Authorize** / **Authenticate** 时，在浏览器里完成登录授权。
4. 如果登录卡住了：在应用上点 **Reconnect**，或者在聊天卡片上点 **Reopen**。
5. 确认它出现在 **Installed** 下。**注意：Added 只表示装好了，不表示已登录**。如果显示 **Needs auth** / **Disconnected**，要再点 **Authenticate**（手机上叫 **Authorize**）。
6. 在聊天里输入 `@` 把连接器挂到任务上。
、（2026-10-12）
- **全账号通用**：装好的连接器所有 Bot 都能用，不能只给某个 Bot。不想让某个 Bot 碰某个邮箱，就只能断开连接。
- **有连接器优先用连接器**，比在网页上点来点去更可靠；只有没有连接器、或者需要看界面的流程才用浏览器。
- 2026-10-02（v0.67.0）起 Marketplace 改名为 **Connect Apps**，按类别分组，**Recommended for you** 排在最前，装得最多的插件也排在前面。把连接器链接直接发给 Bot，它会自动装上目录里对应的插件。
- Bot 可以使用已装插件里最多 200 个技能（以前只有 24 个，v0.71）。连接器调用的超时从 1 分钟延长到 2 分钟。

### 3.2 已知可用的官方连接器（按官方发布时间整理，不是完整列表）
| 时间 | 连接器 |
| --- | --- |
| 发布时 | Gmail、Google Calendar、Drive/Docs/Sheets/Slides、Slack、Notion、Linear、GitHub、Figma、Amplitude、X 等（视频演示里出现过的） |
| 2026-08-31 | Microsoft Outlook、Calendar、OneDrive |
| 2026-09-10 | GTM：Salesforce、HubSpot、Gong、Clay、Granola |
| 2026-09-11 | Microsoft Teams |
| 2026-09-16 | 1Password（在保险库里共享条目，每次填充都要你批准，密钥一直留在密码管理器里） |
| 2026-09-26 | Finance 集成（连接银行、信用卡、投资账户，帮你管理开支） |
| 2026-09-30 | GitHub 插件、Origin 插件（另外可以把编码任务交给 Cursor） |
| 2026-10-08 | Shopify（查订单、跟踪库存、更新商品信息） |
| 2026-10-11 | Glean（需要 Glean MCP URL）、QuickBooks Online；Figma/Notion/Canva 可以在聊天里显示交互卡片；可以用 X Money / Stripe Link 付款 |
| 2026-10-18 | Robinhood、Coinbase、Trello 等（视频口述，**changelog 里还没有**） |
- 有人演示过时已经连了 17 个：Gmail、Calendar、Higgsfield、X、Ramp、Remotion、Clay、ClickUp、Granola、Apify、Supabase、Slack……
- Cursor 里装过的插件/MCP 也能在 Grok Bot 里用（未证实说法称"Cursor 支持的集成、MCP、插件它都支持"）。官方工程师 Lauren：Cursor 和 Grok Bot 的插件是通用的，同一个插件可以两边都装；插件可以同时包含 MCP 和 skills。

### 3.3 各应用能做什么、不能做什么（官方）
- **Google Calendar**：能读你看得到的日历、建/改事件、查空闲时间、回复邀请；**不能**改 Google Meet 设置，**不能**添加 Zoom 链接（Google 的 Zoom 插件对它不可用）。
- **Google Sheets**：只能编辑这个账户本来就能编辑的表格；查找和打开文件要用 **Google Drive** 应用，改单元格才用 Sheets；不能改共享权限。
- **Slack**：以**你连接的那个 Slack 用户**的身份发消息，不是一个单独的 bot 账号；只能在这个用户能发言的频道里发。
- **Gmail**：搜索、读邮件、起草、发送、打标签；**一次只能连一个邮箱**（想同时连个人和工作邮箱不行）。Notion 等部分应用可以连第二个账户。
- **X 插件**：需要你 X 开发者账户里有 API credits（详见 3.5）。

- ⚠️ 关于多账户，视频里有不同的说法：有人演示过多个 Gmail（2026-08-25） 演示"add another Gmail（标签 work）"（2026-09-30），但帮助中心现在明确写"Gmail 一次一个邮箱"。。请以帮助中心为准。

### 3.4 企业/团队常见授权问题（官方）
- **Disabled by team admin**：Cursor 团队管理员控制哪些应用可用，需要找管理员开启。
- **Google 提示"Your admin needs to review Grok"**：Grok Bot 登录 Google 时显示的应用名是 **Grok**（不是 Cursor）。IT 管理员操作：Google Admin console → **Security → Access and data control → API controls → App access control → Manage third-party app access** → 添加 **Grok**（按名字搜不到就用被拦页面上的 client ID）→ 设为 **Trusted**。换浏览器、重连都解决不了 Google 管理员的拦截。
- **Zoom 报错 4700**（Invalid redirect）：已知问题，**目前没有绕过办法**。
- **SharePoint AADSTS50011**：已知问题。团队管理员在 dashboard **Integrations** 选 **Application-level permissions** 时会出现；改用 Delegated 权限。


### 3.5 X（推特）读取：免费内置 vs X 插件
- **内置的公开 X 搜索（2026-10-07 官方公布 ，v0.70.2）**：不用连接 X 账号就能搜帖子、打开帖子、读某个账号的帖子和提及、看谁引用/转发了、统计话题帖量、看趋势和新闻。只读，不以你的身份登录。
- **要读私信、书签、首页时间线、点赞名单**：在 Connect apps 里装 **X 插件** → 用 X 登录；这一部分需要 **X API credits**（在 console.x.com 购买）。2026-08-29 官方说连接 X 账户时会自动给你创建一个带 credits 的开发者账户，但帮助中心说起始 credits 可能用不了（已知问题）。
- 社区经验：
 - 有人让自己的 X Bot 通知其他所有 Bot 把例程改成用内置 X 读取，省掉开发者 credits；Bot 说每天有 1,000 次免费读取（约美中时间 19 点重置），**Nate 本人也说别全信**。（2026-10-09）
 - 有说法称"每天 1,000 次 X 读取、每次 25 条推文"（**未经官方证实**）。
 - 此前有人的每日 X 摘要靠付费 API，烧了不少 credits，内置读取上线后免费了。
 - X 连接器授权了，但 API 调用要另外充 credits。
- 2026-10-18：Bot 可以读并回复 X Chat 私信（有限制，禁止刷屏）。另有说法称 10-12 时当时私信是加密的、读不了。⚠️ 两者时间不同，以最新为准。
- ⚠️ **封号风险**：论坛有人用浏览器自动化在 X 上发帖，账户被冻结。在禁止自动化的网站上，建议用官方连接器/API，不要用主账号做实验。见 12 节

### 3.6 Bot 自己的邮箱（Agent email，2026-10-09 发布）
**领取步骤**
1. 方法 A：在聊天里说 "Get yourself an email address"，按提示选名字；方法 B：**Connect apps → Email → Choose a name → Create address**。
2. 名字规则：小写字母、数字、`.` `-` `_`，最长 64 个字符，不能用保留词。
3. **每个用户只有一个地址**，归你本人所有、所有 Bot 共用；**不能改名、不能删除**；删除账户后地址会退役，永远不会再分配给别人。

- 把官方推文贴给 Bot，说"Go set it up"，它会问你 @ 前面用什么名字；也可以在 X 上 让它帮你设置。
- 建议**用助理的名字，而不是你自己的名字**，方便抄送它来安排会议。

**收信与自动处理**
- 任何人都能给它发信；没通过垃圾邮件/病毒扫描的邮件会去掉正文和附件。**App 里没有收件箱界面，也没有新邮件通知**，只能问 Bot，比如"What arrived in your inbox today?"
- 新邮件**不会自动唤醒 Bot**，要靠例程：可以监听任何发件人、指定地址或整个域名（`*.com`）；默认只处理通过邮件认证检查的发件人；**不能按主题过滤**。
- 带标签的地址（如 `name+shop@`）可以发信，而且只针对这个标签的邮件触发例程（v0.71）。
- 别人邮件里的指令只当作信息，**不算你的授权**。


**发信审批与限制**
- 帮助中心："每一封都要你批准，没有无人值守发送的选项。"
- ⚠️ 矛盾：changelog v0.64.0（2026-09-30）写着草稿卡片上有 **Disable drafts for this Bot**，开启后这个 Bot 直接发送；v0.71.2 也写着"选 Disable drafts 后，从自己的地址立即发信，不再出草稿卡"。
- 限制：每封最多 50 个收件人（To/Cc/Bcc 合计）；附件最多 10 个、单个 10 MB、合计 25 MB，不能附可执行文件；newsletter 和首次联系的邮件每封只能发一个收件人，并自动带一键退订；对陌生人的发信量也有限额。
- 同时连了 Gmail/Outlook 时，它会问你用哪个账户发。
- 团队：Agent email **默认关闭**，管理员在 Cursor dashboard → **Grok Bot → Bot Capabilities → Agent Email** 打开。


**社区用法**
- 把 Bot 邮箱加进邮件往来 → 它查日历给出空档并起草回复 → 你确认后发送 → 说"yes, add the invite"它就加日历邀请；回复 Bot 发来的报告邮件还能触发 Linear 任务。
- 在官方邮箱推出前 用第三方 AgentMail（免费版 3 个邮箱）给 Bot 单独开账号，再把这个邮箱以最小权限邀请进你的应用，不共享你的主账号。（2026-08-17，**已被官方功能替代**）

### 3.7 密钥（Secrets）与 1Password
- **不要把 API key 或密码直接贴进聊天或写进文件**。Bot 需要密钥时会弹出 **Secure secret** 卡片 → 输入 → **Save securely**；值是只写的，存好后谁都看不到原值。Team Bot：详情 → **Secrets → Add secret**。
- 不小心贴进聊天了：立刻去服务商那里**轮换（rotate）**这个密钥。
- **1Password**（2026-09-16）：Bot 可以用 1Password 里的凭据登录、填表、付款。2026-09-08 官方说可以在聊天里完成表单和登录，"support for any password manager"。
- 在 1Password 里建一个**专门共享给 Bot 的保险库**，把 Alpaca 模拟交易的凭据放进去。
- 论坛 Bug：1Password 条目的 URL 太长会出问题，2026-10-20 已修复。见 12 节

### 3.8 自定义 MCP / 第三方聚合器（社区做法）
- **直接把 MCP 配置贴给 Bot**：在 Blotato **Settings → API → other MCP clients** 复制配置 → 贴进聊天说"connect the … MCP" → 弹出卡片确认。Higgsfield MCP 也是类似流程。
- **让 Bot 自己去找**：比如"find the custom MCP server of Buffer and add it"。
- **Zapier MCP**（9,000+ 应用）：Zapier → Create → MCP servers → 客户端选 **Cursor** → 复制 prompt → 贴给主 Bot → 卡片确认 → 在 Zapier 里 Add apps（例如 Drip 的 23 个 actions）。
- **Composio**：一个中心化 MCP 枢纽，可以连 YouTube、Reddit、LinkedIn、Perplexity 等原生不支持的服务，而且换别的 Agent 平台也能沿用。
- **私有网络里的 MCP**：放在 Tailscale 私网上的 MCP 服务器目前连不上，官方建议用 **Tailscale Funnel** 暴露出来。；更多私网方案见
- 社区收录的 MCP/插件清单：

---

## 4. 例程（Routines）

### 4.1 是什么
- Routine 告诉**某一个** Bot"什么时候做事"。两种触发方式：**按时间表**；或者**由事件触发**：Slack、GitHub、Linear、Sentry、PagerDuty、Microsoft Teams、邮件（Bot 自己的邮箱）、webhook。例程会在云端运行，你关了电脑也照跑。、；（Teams/Linear/Sentry/PagerDuty 触发）、（webhook）
- 例程可以写"运行某个 skill"或者"去找这几个 Bot 说话"，所以 Bot 之间可以组合使用。
- 限制：每个 Bot 最多 50 个例程；每个例程保留最近 20 条运行记录；定时间隔**至少 5 分钟**。

### 4.2 创建（官方推荐：直接在聊天里说）
1. 打开"负责这件事"的那个 Bot，在聊天里说清楚做什么、什么时候做，例如"Every weekday at 9:00 AM, summarize new support tickets in this chat."
2. Bot 创建例程后，聊天里会出现一行 "Created routine"，同时它会总结这个例程做什么、结果发给谁（v0.67.0 起）。
3. 打开例程，检查 **When to run** 是否和你想的一样（时间表会用文字显示）。不对就点 **Edit** 告诉它改什么。
4. 用 **Test** 立刻试跑一次。**注意：Test 会真的干活**，会改文件、会用连接器，请用安全的输入。
- 手机上：Bot 资料页 → **Routines** 下的 **Add routine** → 系统会预填 "Set up a routine to "，补全这句话后发送。
- 新建的例程**创建后不会马上运行**，会等到下一个预定时间（比如 8:05 设"每天 8:00"，第一次运行是明天 8:00）。

- 时区：**Settings → Bot → Timezone**（**Auto-detect** 跟随本机）。中国用户如果想按北京时间跑，建议在这里确认，或者在指令里写明时区（例如"香港时间 9 点"）。
- 还可以反向提问："what routines should you have?" / "what are the best routines we can set up?"

### 4.3 查看与管理
- 桌面端：点聊天顶部的 Bot 名字 → **Details** 标签 → **Routines**。每行显示名字和运行时间（或 **Paused**），行上的开关可以暂停/恢复。v0.68.0（2026-10-05）起，**右键例程**有 **Pause / Resume / Test / Edit / Delete**。打开后能看到 **Instruction**、**When to run**、**Webhook**（URL 和 key）；底部按钮有 Pause/Resume、Test、Edit、Delete routine（删除不可撤销）。
- **Edit** 会预填一条消息给 Bot，开头是 "Edit your routine:" 加例程名，补上要改的内容后发送。
- 手机上：Bot 资料页 → Routines → 点开可以看 **Active / Schedule / Next run / Instruction / Run history**；关掉 Active 就是暂停，左滑删除。

- ⚠️ 旧界面：8–9 月的视频里，例程在"右侧面板"（Create routine → name、instruction、trigger），或者"右上角电脑图标旁边"。现在的界面以上面的官方描述为准。

### 4.4 Slack 触发
- Slack 触发是例程的一部分，和 Slack 应用不是一回事。可以在这些情况下触发：Bot 被 @、提到某个词/短语、你对消息加了表情回应、任何新消息；可以只监听一个频道，也可以监听整个 Slack。触发前的旧消息不算。**频道和短语要尽量窄**，不然很费额度。前提：拥有这个 Bot 的 Cursor 账户要先连好 Slack。

### 4.5 Webhook 触发（官方步骤）
1. 让 Bot 给例程加一个 webhook 触发。
2. 在桌面端打开例程，**Webhook** 区有三个字段：**POST to**（URL）、**key**（密钥）、**header**（完整的 `Authorization: Bearer <key>`）。点字段就能复制。
3. 向这个 URL 发 HTTP POST，带上 header，可以附 JSON body；Bot 会连同例程指令一起收到这个 body。
4. 返回 **200** 只表示"已接受并开始运行"，**不表示做完了**，结果要去 Bot 的聊天里看。
```bash
curl -X POST "<POST to 的 URL>" \
 -H "Authorization: Bearer <key>" \
 -H "Content-Type: application/json" \
 -d '{"task":"示例：把这条数据整理进日报"}'
```
（以上命令只是按官方说明写的示例格式）
- 论坛员工补充：找 webhook URL 的方法是点 "Created routine" 那一行里的例程标签；想**轮换 key** 只能删掉例程重建；想停用就暂停。见 12 节
- 社区用法：
 - 让 chief of staff 新建一个 Bot，配一个 webhook 触发的例程（"inbound command webhook"）和一个 skill，这样就能从 ChatGPT / Claude Code 给 Grok Bot 下指令。
 - 表单应用把数据 POST 过来 → Bot 给提交人发邮件，并在 ClickUp 里通知。
 - 社区桥接项目：微信/飞书/本地 Agent/GitHub webhook 转发（Cloudflare Worker + HMAC），见 13 节。

### 4.6 额度与频率（很重要）
- 每次运行都消耗额度。每小时一次、很短的间隔、或者监听热闹频道的 Slack 触发，**一天就能用掉一周的额度**。额度掉得快时：先暂停不需要的例程，再让 Bot 放宽时间表、收窄 Slack 触发，最后才考虑升级套餐或提高按需上限。从不运行的例程不花钱，但 **Test 花钱**。
- 官方团队的建议："永远不要每 5 分钟跑一次"，一般每小时一次或一天几次就够了；先问自己"真人同事能做好这件事吗？"做不到就先优化。
- Peng 的 Dr. Eggbot 会定期检查所有 Bot，提醒"这个例程太频繁、很费额度"。
- 论坛：用量里的 "Free" 行是后台检查，"Included" 行是例程。见 12 节
- 有实测：每天一次的调研例程，每次大约跑 10 分钟。

### 4.7 例程没跑 / 失败排查（官方清单）
1. 是不是 **Paused**（手机上看 **Active** 有没有打开）。
2. **When to run** 的时间表和时区对不对。
3. 新建的例程要等下一个时间点，想马上跑就点 **Test**。
4. Slack：是不是新消息、频道对不对、短语是否匹配。
5. Webhook：有没有返回 200，key 是不是最新的。
6. 报 "Automation run rate limit exceeded"：这是所有 Grok Bot 例程共享的平台限制，不是你自己额度的问题；**不会自动重试**，要手动 Test 或重新调用 webhook。
- 你长时间不在、没有回应时，例程可能被**自动暂停**（官方说明 + 社区实测）。
- 2026-10-08 有一批例程被延迟执行，错过的运行**不会补跑**。见 12 节
- 例程运行中需要登录某个网站时，会在那一步到聊天里问你，而不是悄悄停下（v0.71.2）。

### 4.8 例程实战例子
| 例子 | 做法 |
| --- | --- |
| 每日 AI 视频工具调研 | 四行 brief 跑通后，一句话："每个工作日早上 9 点香港时间按上面的 brief 做一次，结果放在这个对话，不要发邮件，找不到就写找不到"→ Bot 自己选了 8:50 跑，好让你 9 点前收到；连续 13 天准时 |
| 新闻晨报 | Teach a task 录下浏览 Hacker News / OpenAI News / xAI News 的过程 → "Save that as a routine. Every morning at 8:00 a.m. …" |
| Slack + 邮件晨报 | "Every morning at 9:00 a.m., look through my Slack and email and send me a report." |
| 会议待办 | Granola 插件 + 例程 "Granola action item sweep"（工作日 10/13/16/19 点）→ 把待办分给各个 Bot、更新 ClickUp、只有变化时才发摘要 |
| 个人 CRM | X → Notion，每天挑出 90 天没联系的 10 个人，并附上开场白 |
| 社交监控 | "Potato" Bot 每 30 分钟检查 X 提及，汇总 bug 报告给工程 Bot |
| 赞助邮件 / SEO | 每天扫赞助邮件，按规则起草回复（发送前审批）；每周 SEO 检查 |
| 备份整理 | "Overwatch" Bot 定期整理并备份到 GitHub |
| 额度体检 | 社区配方"Routine 额度体检"（**待验证**） |

---

## 5. 技能（Skills）与 Teach a task

### 5.1 保存一个技能（官方）
- 技能保存的是：步骤、判断规则、预期产出、安全边界。**所有 Bot 都能用**，不过用之前可能需要先连好相关的连接器或登录。
- 官方示例：在完成一次任务后说 "Save the process we just used as a skill called 'Weekly account health.' Include the source systems, risk definitions, output format, and the rule that customer contact always requires approval."
- 好技能要写清楚六点：①什么时候用 ②需要什么输入和权限 ③工作顺序 ④怎么验证结果 ⑤返回什么 ⑥哪些操作需要审批。
- 在哪里看：**Connect apps → Your plugins → Manage plugins and skills → Private skills**（私有技能是一个库，所有 Bot 共享）。

- 社区说法一致："好技能 = 什么时候用 + 准确顺序 + 出错时怎么办"，可以像菜谱一样不断根据反馈改进。
- 论坛员工：桌面详情面板里看不到技能时，可以用 `Cmd+Shift+M`（Connect apps）→ Private skills 编辑。

### 5.2 调用技能
- docs：在桌面输入框输入 `/` 引用技能；如果 `/` 菜单里没有，就去 Private skills 检查。
- ⚠️ changelog v0.68.1（2026-10-07）："Typing / in the composer no longer opens a menu of skills and actions."，所以现在更可靠的做法是**用自然语言点名**（比如"用 Weekly account health 技能跑一遍"），或者用 `@`。
- 例程的指令里可以直接写"run this skill"。

### 5.3 Teach a task（演示教学）
**官方步骤**
1. 打开一个**一对一**的 Bot 对话，并打开它的电脑画面。
2. 选择 **Teach a task**。
3. 描述你接下来要演示的结果。
4. 完整操作一遍流程。
5. 停止录制，检查 Bot 生成的技能。
6. 先用安全的例子测一遍，再安排定时。
- 最多录 **10 分钟**，**不录麦克风声音**。演示时不要暴露密钥，凭据用安全交接流程。
- 学出来的技能是**草稿**，要自己补充判断规则、失败处理和审批边界。
- 这个功能是逐步开放的，看不到按钮的话，就让 Bot 根据书面说明和已完成的任务来生成技能。

- 社区实操：有人点 "Teach task" 开始录制 → 访问 Hacker News（遇到 CAPTCHA 他自己手动解了）、OpenAI News、xAI News → 停止 → 补一句提示 "Save that as a routine. Every morning at 8:00 a.m., check …"，结果直接生成了例程。（2026-09-06）
- ⚠️ 结果是 skill 还是 routine？docs 说 Teach a task 产出 **skill** 说"示范一次就存成 routine" 是在录制后**额外要求**存成例程。理解方式：录制 → 技能（草稿），再由你决定要不要做成例程。
- 社区配方"Teach-a-Task"（**待验证**）：

### 5.4 导入别人的技能
- 把技能文件拖进聊天："read this skill, make a new skill … use this on me to learn about my business"，它会保存下来，之后说"grill me about X"就能调用（Grill Me 技能）。技能存放位置：Plugins → Yours（private plugins）。
- Connect apps 里有些插件会附带打包好的技能，例如 X 插件里就带一个 skill。；Lauren
- 社区技能合集：（"技能"一节）

### 5.5 "信任阶梯"：手动 → 技能 → 例程（官方团队做法）
- Peng / Lauren：先手动做 → 观察并纠偏 → 能一次成功后存成技能（例如 `/expense-report`）→ 再设成例程。"信任是一点点建立的。"
- 官方 docs 的"可信例程设计"：先自动化准备工作，再自动化执行；先让 Bot 起草、核对、给建议；发送、购买、删除、发布、改生产系统都要审批。

### 5.6 记忆（Memory）与长期规则
- 长期规则让 Bot"记住"，例如 "From now on, ask before sending any email."；一次性要求就正常发消息。
- **复制 Bot 不会复制记忆**；Team Bot 会提示你哪些记忆分享给团队（**Share with team** / **Keep private**）。
- 官方提醒：Bot 的记忆**不能代替正式资料来源**。
- 论坛员工：真正关键的约束请用 Auto-review 的 **Ask first** 规则，不要只依赖记忆；记忆是新的优先，所以定期让 Bot 合并/整理它的规则。见 12 节
- 社区常见做法：让 Bot 把共享知识记在 /workspace 的文件里，或者建一个专门的记忆/日志 Bot；所有事情都记到 Sheet/ClickUp/数据库，每次例程运行也记一条。

---

## 6. 云电脑与浏览器

### 6.1 一台共享电脑（⚠️ 最常见的误解）
- **官方**：每个用户有一台持续运行的云电脑，**所有 Bot 共用**。浏览器的 cookie/登录、文件、命令行凭据都是共享的；每个 Bot 有自己的屏幕，可以并行工作，但**屏幕不是安全边界**，不能靠它隔离权限。团队里每个成员也是一人一台。
- ⚠️ 不少社区教程写成"每个 Bot 有自己的电脑"（x.ai 的 FAQ 也有 "Bots have their own computer" 这种容易误解的说法、eptwts、早期版本都这么写）。以 docs 为准：**一台电脑、多块屏幕**。
- 实际影响：给一个 Bot 登录了网站，其他 Bot 也都能用这个登录态；不想让某个 Bot 碰某个账号，靠"不同 Bot"是隔离不了的。要用 Auto-review 规则、断开连接器，或者用 Team Bot（Team Bot 在 Slack 频道等场景会用单独的共享电脑）。
- 屏幕分辨率从 1280×800 提升到 **1920×1200**（v0.68.1）。

### 6.2 文件
- 共享目录是 `/workspace`。让 Bot 把长期要用的项目文件放在这里，并用清楚的项目文件夹。
- 临时目录、手动装的软件包、没提交的应用状态**都可能丢**；重要结果要复制到 /workspace 或者作为附件发到对话里。
- 帮助中心：`node_modules`、`venv` 这类东西不会被保存。
- ⚠️ 论坛反馈：出现过快照**悄悄回滚**的情况（包括磁盘满的时候），**重要的东西请在云电脑之外另做备份**（GitHub、Drive 等）。见 12 节 的 "Overwatch" Bot 专门负责备份到 GitHub
- 附件上限：⚠️ docs 写单条消息最多 6 个附件、单个 25 MB、视频 200 MB；changelog v0.71（2026-10-14）写**单个 250 MB、视频 1 GB、每条消息 20 个、合计 200 MB**（需要 0.70.2+）。以较新的 changelog 为准。
- 磁盘快满时，Bot 会提醒你一次，列出最大的文件，**你同意后**才删（v0.71）；还有"Disk Saver"清理方案。
- Bot 能做 PPT（直接出 PowerPoint 文件或 Google Slides，v0.68.1），也能把网页存成 PDF、发 MP3/M4A/WAV 音频（v0.70.2）。分享出去的页面带 **Download**（存成 PDF）。

### 6.3 浏览器登录、接管（Take over）与验证码
- 浏览器登录状态会一直保留，一般不用每次都登。遇到会话过期、二次验证时，**让 Bot 停下来通知你，不要让它去绕过**。
- **接管流程**：电脑卡片上出现 **Action needed** → **Take over** → 你自己输入密码、2FA、过 CAPTCHA → **I'm done**（或 **Skip**），Bot 会在聊天里接着回复（v0.71.2 修复了它有时不说话的问题）。Okta/SSO 也是这样操作。**Passkey 和生物识别在 Bot 的电脑上用不了**。、
- 收到邮件验证码或登录链接时，Bot 会从你连的 Gmail/Outlook 或者它自己的邮箱里读，不用再问你（v0.70.2）。
- **Secure Form**：结账、表单的敏感字段会弹出安全表单让你填，不会进聊天；表单可以预填已知字段（v0.71）。
- 购物：2026-08-28 起可以连接 Link 让 Bot 在网上买东西；v0.70.2 支持 X Money / Stripe Link 付款。

### 6.4 Bot 电脑的命令行（社区玩法）
- 云电脑有终端，可以装 CLI。有人在 Bot 电脑的终端里装了 Claude Code / Codex CLI → 用 Take over 登录自己的订阅 → clone 自己的仓库，并建了名叫 "Claude Code" / "Codex" 的 Bot。（2026-10-09）⚠️ 第三方订阅的服务条款请自行确认。
- 社区"本地 Agent 桥接"：在云电脑里装本地 Agent 干重活，干完自动回报。（2026-10-18）；对应的提示注入风险提醒 （2026-10-19）

### 6.5 让 Bot 用你自己的电脑（本地执行）
- 云电脑和你面前的 Mac/Windows 是分开的。只有开启了本地执行能力、并且你按策略批准后，Bot 才会在本机运行命令。
- 策略设置：**Settings → General → Bot → Execution on Local Computer**：**Ask every time**（默认）/ **Always allow** / **Never allow**。注册了多台电脑之后，这个设置移到 **Settings → Computer → Computers**，可以分别设置；v0.68.0 起可以在这里 **Remove** 别的电脑。
- 官方建议：如果 Bot 不需要读你本机的文件，就选 **Never allow**（。读本机文件需要桌面端在线，但云端干活不需要你的电脑开着。
- 你的电脑睡眠后，Bot 会在 2 分钟内把它当作断开（v0.71）。

### 6.6 经你的电脑转发流量（Route traffic through this computer，v0.50.0）
- 让 Bot 浏览器的网页流量从你的电脑出去（用于只允许你本地 IP 访问的网站）。论坛员工澄清：**只转发浏览器网页流量**，不转发终端命令。团队管理员可以关掉这个功能。、；见 12 节
- 中国用户的考虑：开了它，Bot 的网页流量会从你所在的网络出去。有社区作者建议关掉，另一位作者认为长期用大陆 IP 操作会让出口被标为中国。这些都是个人经验，**官方没有给中国地区的建议**。
- 企业代理/私网：

### 6.7 电脑出问题时的恢复顺序（官方：从影响最小的开始）
1. **Retry**
2. 退出并重新打开 App
3. **Settings → Computer → Bot's computer → Update Bot's Computer**（装最新软件，文件保留）
4. **Recover computer**（**只有在错误状态下才会出现**，确认框叫 "Recover Grok Bot's Computer"，Settings 里没有这个按钮）
5. **Reset Bot's Computer**（用最近一次快照重建，最近的改动可能丢失）
- 尽量等正在进行的工作结束后再恢复。
- 电脑睡着时，点 **Computer is asleep** 预览就能唤醒（v0.71）；用手机打语音电话也会唤醒它。
- 企业管理员可以在 dashboard 的 **Manage Member Computers and Data** 批量 Recreate/Terminate 成员的电脑（两种操作都保留持久磁盘），也可以设置 30 天不用就自动终止。

---

## 7. 语音与渠道

### 7.1 实时语音对话（Voice chat，2026-09-17 上线）
**官方步骤**
1. 打开和某个 Bot 的聊天。
2. 保持输入框**为空**（不为空时不会显示语音按钮）。
3. 点 **Start voice chat**（输入框里的波形按钮）。
4. 系统询问时允许麦克风权限。桌面显示 **Connecting…**，手机显示 **Calling…**，然后 Bot 开始说话。
- 通话中可以：**Mute/Unmute**、**Hang up**、**Show transcript**、**Show settings**（**Voice** 声音 / **Speed** 语速 / **Language** 语言，**Auto-detect** 会根据你说的话自动判断）。通话期间可以去看别的聊天，再点 **Return to the voice chat** 回来。
- **同一时间只能有一个通话**；**群聊里不能语音**；在桌面上，可以和同事的 Team Bot 在你自己的聊天里语音。
- 挂断后，Bot 的聊天里会出现一张 **Voice chat** 卡片，显示时长，点开可以看完整文字记录；如果还有没做完的事，Bot 会接着在聊天里跟进。
- 掉线时显示 **Voice chat failed** → **Dismiss** → 检查网络 → 重新开始；麦克风在 **Settings → Microphone** 里选。v0.71 起，掉线会**自动重连**并继续同一个对话。
- **语音输入（不是对话）**：桌面点 **Start voice input** 或按 `Cmd/Ctrl+D`（也可以按住说话）；手机点 **Start dictation**。说完会变成文字放在输入框里，Bot 用文字回复。
- **语音便条**：2026-09-18 起 Bot 可以给你发语音消息。
- **iPhone 操作按钮**：2026-10-19 起可以把 iPhone 的 Action Button 设成 Grok Bot，按一下就和 Bot 说话。；Android 上可以把 Grok Bot 设成默认助手
- 社区经验：
 - 默认语速偏慢，他用 1.25–1.5 倍；用语音让主 Bot 把邮件分拣交给邮件 Bot；涉及登录时 Bot 会拒绝替你输入密码；语音有时不听"别说了"的指令，他觉得 ChatGPT 的语音模式更好。（2026-09-18）
 - 可以直接开口打断它；通话中它会去搜你的其他消息；挂断后 30 分钟内它还记得这次通话的内容（10-18）。
 - 散步时用 iPhone 给主 Bot 打电话派活。

### 7.2 移动端（iOS / Android）
- 要求：iOS 18+（iPadOS 18+ 也行），Android 9+。登录：**Log In or Sign Up** → 浏览器认证。
- 能做的事：发文字、语音输入、语音对话、播放语音便条、拍照/发图/发文件、在群里 @ Bot 或 ``、线程回复、加表情回应、编辑和发送邮件/Slack 草稿（**Send / Discard**）。
- iPhone 的系统分享菜单 → **Grok Bot** → 选聊天 → **Attach**（照片、文件、链接、文本都可以）；**Android 的分享菜单目前只支持文本**。
- 首页 **+** → **New Bot** / **New Group Chat**。可以查看电脑画面、Take over、**I'm done**。
- **例程在手机上只能查看、暂停、删除；改时间表、改指令、Test 都要用桌面端**。电脑的 Update/Recover/Reset 也只能在桌面上操作。
- 搜索范围：Messages / Bots / Group Chats / Files / Routines / All。

- **不要在手机上重复购买**：用同一个 Cursor 账户登录，额度会自动带过来（X Subscription 的 Premium/Super/Ultra、旧 Cursor 套餐、SuperGrok 链接、X Premium+ 链接都算）。
- **通知**：用桌面时，Bot 的回复和提问会通知到桌面，不发到手机（需要 0.70.2+）；Bot 需要你处理时，大约 3 分钟后才会通知手机。没有"只在桌面开着时手机静音"的开关；想关某个 Bot 的通知，点它的名字关掉 **Notifications**。、

### 7.3 Slack
- **Slack 应用（连接器）**：以你本人的 Slack 身份发帖（见 3.3）。
- **草稿卡片**：Bot 起草的 Slack/邮件消息会在聊天里显示成卡片，可以编辑后点 **Send message** / **Send**，或者 **Discard**（2026-09-09 上线 inline drafts）。修改后的部分会高亮（10-18）。有人演示过："draft up an email to the top 10 emails I need to respond to" → 一次出 10 张卡片。、；
- **Slack 里的 Bot 能不能互相派活**（官方表格）：
| Bot 类型 | 在哪里跟它说 | 能派活给其他 Bot 吗 | 回复出现在哪里 |
| --- | --- | --- | --- |
| 个人 Bot | App 里，或你和它的 Slack 私信 | 能 | App 的聊天里，不在 Slack |
| 个人 Bot | Slack 频道线程 | **不能**（线程是共享的） | Bot 会说做不到 |
| Team Bot | Slack 频道线程或私信 | 能，派给"@它的那个人"自己的 Bot | 同一个 Slack 线程 |
 想在 Slack 频道里让 Bot 之间交接工作，就把它做成 Team Bot 再加到 Slack（见 8.4）。
- **Slack 触发例程**：见 4.4。
- Slack 里的审批卡片：Team Bot 会发 **Review an action**（**Allow once / Deny / View details**），只有发起请求的人能处理（v0.63.0）。

### 7.4 在 X 上 （Tag on X）
**前提**
1. 在 grok.com 的 **Settings → Account** 里连接 X 账户。
2. 你自己要有一个 Bot（请求会交给你的 primary Bot）。
3. 不支持的地区：澳大利亚、英国、欧盟、冰岛、列支敦士登、挪威（按 X 记录的账户国家判断）。
**行为**
- 在帖子里 并说你的请求，Bot 会接手处理。如果帖子里**同时 @ 了 ，或者包含视频/GIF**， 不会回复。
- 回复里出现 "Sign up for Grok Bot…" 说明你还没有 Grok Bot 账户；出现 "Grok Bot isn't set up yet…" 说明还没设置好。

- 有人演示过这个流程：在推文下 让它收藏或调研，结果回到 App 里。 说在线程里 需要装好并配置 X 插件、X 用户名要关联到账户（10-18）。

### 7.5 邮件渠道
见 3.6（Bot 自己的邮箱）。用 Gmail/Outlook 连接器发出的邮件是以你本人的身份；用 Bot 邮箱发出的是以 Bot 的地址。有多个账户可以发时，Bot 会先问你用哪个，并告诉你实际从哪个地址发的。

### 7.6 社区桥接：微信、飞书、本地 Agent
官方没有微信/飞书渠道。社区有基于 webhook 例程的桥接项目（**第三方，请自行评估安全性**）：
- 微信桥（iLink + webhook）、飞书桥（lark-cli）、本地 Agent 桥（Pi）、GitHub webhook 转发器（Cloudflare Worker + HMAC 签名）等，见 "桥接"一节：
- 本地 Agent 和 Grok Bot 打通：本地 Agent 写任务文档 → 推到仓库 → GitHub Action 调用 Bot 的 webhook。

---

## 8. 多 Bot / 群聊 / Team Bots

### 8.1 Bot 之间的交接（Handoff）
- Bot 可以给其他 Bot 发消息、交接工作，这是**异步消息**：对方会在自己的对话里处理，结果再传回来。
- 说 "Stop now" 会结束工作，**但不会撤销已经完成的操作**；告诉 Bot 停止，也会一并停止它交接出去的 Bot 和它启动的 Cloud Agents（v0.56.0）；v0.71 起还会结束它在后台跑的子代理。

- 论坛员工补充：Bot 说"稍后会做"的事**不会真的被排进日程**，要做成例程才会执行；不紧急的 Bot 消息**不会唤醒**另一个 Bot。见 12 节
- 团队管理员可以在 dashboard 的 **Bot capabilities** 里关掉 **Messaging between Bots**（v0.71）；个人用户在群聊里没有开关可以阻止 Bot 互相发消息。

### 8.2 群聊（Group chats）
**创建**
1. 桌面：**New** → 选 2–6 个 Bot。手机：**+** → **New Group Chat**。
2. 用 `` 指定某个 Bot；**Reply** 回复某一条，回答那个 Bot；`` 少用（每个 Bot 的回复都算用量）。
**限制**
- **审批请求、密钥/登录请求、邮件/Slack 草稿不会出现在群聊里**（要去和那个 Bot 的一对一聊天里处理）。
- Bot 交接到群聊时只传文字。
- 群里不能语音。

- 在群里说"hey"会让每个 Bot 轮流回复，没意义；更好的做法是让**一个 Bot 来调度**，例如"看看 product expert 那边上线了什么，再用我的口吻写条推文"。群聊内部机制：调度者不是成员之一，由主持者轮流唤醒每个成员。
- 七七行銷筆記：建好多个专员 Bot 后，拉进同一个群当"AI 团队"，分工完成同一个目标。（2026-10-08，繁中）

### 8.3 主 Bot（Primary Bot）与"幕僚长"模式
- v0.66.0（2026-10-02）：名字旁有星标的就是主 Bot，可以**主动**提出能帮你做的事（2026-10-01 官方公布 ）； on X 的请求也交给它。Team Bot 不能当主 Bot。
- 任何 Bot 都能变成主 Bot，直接问它"Hey, can you now become the primary bot?"；主 Bot 会自动把任务分派给其他 Bot。
- **幕僚长（Chief of Staff）模式**（社区常见、官方团队也在用）：一个总管 Bot 负责分派任务；Peng 的工程主管 Bot 在描述里写明"**永远不要自己干活，总是委派给这几个 Bot**"，还附上了 Bot ID。 建了一个叫 "Elon" 的幕僚长 Bot，喂给它目标后，它自己建了 Research / Copy / Studio 等 Bot。
- 官方博客（Eric Zakariasson，实验性模式）：**一个项目 = 一个频道 + 一份名单 + 一块 Notion 看板**。建一个 Projects Manager Bot，用 "Project Ops" 技能开项目、开频道；用人规则：①优先复用现有 Bot ②除 PM 外最多 5 个 ③只有没人能干、并且你同意时才新建。卡住的任务标成 *Blocked* 并在频道里 @你。

### 8.4 Team Bots（团队共享 Bot，v0.61.0，2026-09-26）
**谁能用**：Cursor 团队里有 Grok Bot 的成员。Team Bot **永远在云端运行**；每个人和它的对话都是私密的，所有者和 Manager 也看不到。
**新建**
1. `Cmd/Ctrl+K` → **Create new Team Bot** → 系统建一个 "New Team Bot" 并打开聊天（找不到这个选项，说明你不在 Cursor 团队里，或者 App 需要更新）。
2. 用一句话告诉它要帮团队做什么。
3. 按它的引导设置应用、密钥、技能和文件（**只放你愿意全团队使用的 key 和文件**）。
4. 点顶部的名字改名，在 **About** 里写描述；在这之前它的状态是 **Draft · Only you can see it**。
5. 分享（见下面）。
**把已有的个人 Bot 变成 Team Bot**：点名字 → **Share → Share with your Team** → 它会另外复制出一份团队版，并引导你选择带上哪些应用、密钥、技能、记忆（**Share with team / Keep private**）、例程（可以移过去或留在个人版）、文件。个人版继续正常工作，聊天记录不会转过去。
**分享**：出现 "ready for your team" 卡片时点 **Publish to team** → 然后点 **Copy link**。如果要限制谁能用：**Share this Bot → Who has access** → 选整个团队，或 **People you invite**（需要 **Limit access** 确认）；**Add people or groups** → **Collaborator**（可以聊天）或 **Manager**（还能改配置、分享）。取消分享用 **Unpublish**（只有所有者能操作）。
**同事怎么找到它**：命令面板 → **Team Bots** → **Add**；手机上搜索 → **Team Bots**；或者打开同事发的链接 → **Start a chat**。（Team Bot 的链接打开的是**同一个**共享 Bot；模板链接是给每人一份**副本**，两者不同。）
**用谁的应用和额度**：Team Bot 用所有者添加的应用和密钥；在你自己和它的聊天里，它也能用你的应用，但会先弹 **Allow** 卡片（**Allow once / Always allow / Skip**）。在 Slack 频道、线程和群聊里，它只用所有者的应用。**每次回复都扣"它在回复的那个人"的额度**。想清除"总是允许"的记录：**Settings → General → Team Bots → Clear connector preferences**。
**用哪台电脑**：在你自己和它的聊天里（包括 Slack 私信），通常在**你自己的** Grok Bot 电脑上干活；在 Slack 频道、线程、群私信和群聊里，用它自己的一台共享电脑。
**加到 Slack**：所有者或 Manager → 点名字 → **Bring to your team's Slack → Connect**（发布前显示 **Connect after publishing**；如果显示 **Awaiting admin approval**，需要 Slack 管理员批准）。每个同事要先在 **Settings → General → Team Bots → Link** 关联自己的 Slack 账户。
**编辑/删除**：详情里有 **Plugins / Secrets / Skills / Files / About / Share**；删除（右键 → **Delete**）会对整个团队删除，同事的聊天和例程也一起没了，**不可撤销**。
**手机上**：可以聊天、处理 Allow 卡片；新建、分享、编辑、连 Slack 都要用桌面。
- Team Bot 新建时是"干净"的（没有插件），不像个人 Bot 会自动共享你的全部插件和技能；Gmail 这类登录型插件按每个同事分别连接；例程留在个人那边。建议流程：新建 → 在 App 里配置、测试 → 再发布并连 Slack。（2026-10-05）
- ⚠️ 区别：对个人 Bot 说"join the Riley bots team"只是把它移到侧边栏的一个分组里，不会变成 Team Bot。

### 8.5 模板（Template）与市场（Marketplace）
- **分享模板**：分享菜单 → **Create template** → 选 **Public link** 或 **Team-only**（Enterprise 默认 Team-only）→ 对方在 x.ai 上看到预览 → **Add to Grok Bot**，得到一份属于自己的副本。
- **从市场添加**： → **Add**；或者打开某个 Bot → **Import Bot**，导入后 Bot 会自己跑设置流程（问你信息、请你连应用、建例程）。官方首个市场模板是 Haggle Bot（采购谈判，2026-09-04）。演示导入某人的 "Overheard"（汇总 Reddit/HN/新闻/X 上对你的提及，工作日发摘要）和 "Dr. Eggbot"（帮你设计 Bot 的 Bot）
- v0.70.2 起，你描述需求时，Bot 会帮你找合适的模板，并在聊天里发链接。
- 官方模板指南：
- 旧界面流程（2026-09-08）：右上角下载图标 → **Share as a template** → Bot 收到 "Create a template of yourself…" → **Publish** → **Copy link**。⚠️ 他提到模板可能带上集成的凭证（比如 Higgsfield），**分享前一定要检查敏感信息**。
- 社区模板目录：的"官方市场 / 模板目录"一节 ；社区学习站 usegrokbot.com、bot.tiezhu.si、cnrcode.com/guides（第三方，见 13 节）

### 8.6 官方团队的 Bot 阵容（参考）
- **Peng（设计师）& Lauren（工程师）的 14 个 Bot**（2026-09-27 访谈）：
 - 幕僚长：路由所有任务
 - 设计师 Bot：通过 Figma MCP 直接改 frame，用技能记住设计系统（"我只做前 5%：搭好系统 + 一个关键帧，剩下让它扩展成完整流程"）
 - Dr. Eggbot：审查所有 Bot
 - 工程主管：只分派、不干活
 - Suki：用 Navan 订差旅
 - Potato：每 30 分钟查一次 X 提及
 - Podcast Bot：把文章链接转成播客音频
 - Crumb：清理磁盘

- 官方工程博客：5 个工程 Bot 各管一块（iOS 共享层、桌面客户端和 CI/CD、基础设施、Android、harness），每个都能开 Cursor cloud agents（见 9 节）。
- 各岗位的官方指南（销售、GTM、营销、法务、招聘、客服、PM、设计……）：

---

## 9. 写代码与云端代理（Cursor Cloud Agents）

### 9.1 基本模式：Bot 当"外环"，Cloud Agent 当"内环"
- Bot 可以把编码任务交给 **Cursor Cloud Agent**（在单独的电脑上运行，受你团队现有的 Cloud Agent 控制，管理员可以禁止 Bot 启动它）。Cloud Agent 完成后，Bot 会把最终报告里引用的图片、视频等文件复制回自己的电脑，附在回复里；桌面 App 里 Cloud Agent 的卡片也会显示这些文件。
- 2026-09-30 官方："Bots can hand off coding tasks to Cursor, manage your PRs with GitHub and Origin plugins, and share video demos."
- **Projects（v0.57.0，2026-09-18）**：你提出要求时，Bot 可以开一个 Project，也就是一个能自己规划工作、运行自己的子代理的 Cloud Agent。 演示了 Bot 给 Cursor project 发消息、触发云端/本地代理，以及 Vercel 预览部署。
- **"外环 / 内环"**：Bot 从 Notion/Slack 收集上下文、写好 prompt，交给 cloud agent 执行，这样能保护 Bot 自己的上下文。

### 9.2 官方工程团队的完整流程（Grok Bot for Engineering）
1. 每个工程 Bot 只负责一个领域（专注时表现最好，因为它带的规格和设计原则更清晰）。
2. 收到任务（来自你或 Slack）→ 启动 cloud agent，调用你的技能，附上详细 prompt，**写明需要什么证据（proof）**。
3. Bot 盯着 cloud agent 的过程记录和产出（截图等），可以排队追加消息，出问题时打断；任务完成后通知你。
4. **闭环反馈**：cloud agent 会截图，Bot 用多模态能力确认视觉改动是否到位，不对就打回。
5. **突破上下文限制**：每个工程 Bot 维护一个共享的 Notion PR 数据库，每 30 分钟检查 Bugbot 评论/安全发现、CI 失败、合并冲突 → 有问题就让 cloud agent 去修，并把状态移回 "Working"；没问题就标成 "Ready for Review"，并自动发起代码审查；**审查置信度高、影响范围小的 PR 会自动合并**，其他的等你回来再看。
6. 可以在自己的 worker 机器上跑 cloud agent（例如闲置的 Mac mini，或者 Cursor Cloud private worker，用于需要 VPN 或 iOS 模拟器截图的场景）。

- 官方数据（自述）：Lauren 一个月提交了 2,000+ 个 PR；Grok Bot iOS v0 三周完成，"只用 Grok Bot"。

### 9.3 GitHub / Origin / 代码预览
- GitHub 连接（v0.52.0）、仓库访问（v0.49.0）、GitHub 插件和 Origin 插件（2026-09-30）。GitHub 看不到某个仓库时，Bot 会停止重试，并给你一个去 GitHub 授权的页面链接（v0.71）。
- 鼠标悬停在 PR 链接上会显示状态和标题的预览卡片（v0.68.0）；详情面板的 **Tasks / Coding** 列表会显示 cloud agent 的运行情况。
- 详情面板的 "Coding" 标签可以打开 Cursor 看代理在干什么；Cloud Agent 的打开按钮可以选 **Open in Web** 或 **Open in Cursor**。
- 社区玩法：
 - "God mode"：技术 demo Bot 每天读你的 X 书签 → 问你"要不要做个 demo？"→ 启动 Cursor cloud agent → 构建、验证、截图 → Cloudflare 预览部署。10x Builder ×
 - 一小时用 Grok Bot 做出每日 AI 热点新闻站（抓 X 热点 → 生成日报 → 定时发布到 GitHub Pages）。
 - "云端定计划，本机写代码"：云端 Engineer Bot 和你一起定计划，本机 Agent 写代码再回来审查。
 - 早期的 grok-ship 项目已被 Firstmate 模板取代（见 13 节）。

### 9.4 Cloud Agent 的成本与上下文
- 论坛员工：用量里每次调用显示 50K–165K token 是正常的，因为每次都会重读指令、记忆、工具列表和最近的聊天（大部分走缓存，更便宜）。鼠标悬停在 token 数上可以看到输入/输出/缓存读写的拆分。省用量的办法：减少事件触发的唤醒、减少 Bot 数量。Cloud agent 可以用仓库里的 `AGENTS.md` 和 `.cursor/CLOUD.md` 来控制上下文。见 12 节
- 新的 X Subscription 套餐里，第三方模型按 API 价格计费（见 2.1）。

---

## 10. 高阶技巧（官方建议 + 社区最佳实践）

### 10.1 审批与安全（必读）
- **审批卡片**：**Allow once / Always allow / Deny**。后台任务的审批请求大约 10 分钟后过期。不可撤销的操作（发送、发帖、购买、删除）执行前，Bot 会检查你自己的消息里有没有批准（v0.70.2）。
- **Auto-review**：由一个独立的审查模型，在 shell 命令、插件调用、电脑操作、自动化写入（改例程/触发器）、委派给 Cloud Agent 之前做评估。可以加 **Custom Rules**：**Ask first**（先问）/ **Allow automatically**，**Ask first 优先级更高**。⚠️ 设置路径的说法不一样：docs 写 **Settings → General → Auto-review**，帮助页写 **Settings → Bot → Auto-review**（开关 + Custom Rules）。论坛员工（2026-10-19）说 Auto-review 的 Ask first 是"唯一真正能暂停动作的设置"。、
- **硬件安全密钥**：**Settings → General → Security Key**。
- 最小权限原则（社区共识）：
 - 只读连接器优先
 - 重要账号别拿来做实验
 - 邮件、付款、发帖一律设 Ask first
 - 分享模板前检查有没有带上凭证。
 - **提示注入**：别人邮件里的指令只当作信息，不算你的授权。社区提醒别让云端 Bot 直接控制本机 Agent（可能有提示注入和跳过权限的风险），云端只负责交任务说明。
- 安全 FAQ 与企业控制：、

### 10.2 省额度
1. 例程别太频繁，每小时一次或一天几次就够了；Slack 触发要尽量窄（官方）。
2. **一个任务开一个干净的对话**：每次调用都会重读对话，所以长对话更贵；不过闲置的长对话本身不花钱（论坛员工）。
3. 删掉不用的专员 Bot：已删除的 Bot 不会被唤醒，闲置的 Bot 还可能被别的 Bot 叫醒。"stay quiet"这类指令只是提示。
4. 少用 ``，群聊里每个 Bot 的回复都算用量（官方）。
5. 在用量里鼠标悬停看 token 构成，减少事件触发的唤醒。
6. 让 Dr. Eggbot 这类"审计 Bot"定期检查例程频率。
7. 查看用量：**Settings → Usage & Billing**；按需付费：**Settings → Usage & Billing → On-demand monthly limit**（旧 Cursor 套餐在 cursor.com/dashboard → Spending 设卡），X Subscription 在 grok.com/dashboard/billing 里设置。
- 官方在 2026-08-26、09-05、10-02 三次**全员重置**过每周额度。、

### 10.3 Bot 设计（社区高频技巧汇总）
| 技巧 | 说明 |
| --- | --- |
| 一个岗位一个 Bot | 按"角色"而不是"任务"来建（官方 docs） |
| 反向提问 | "Based on what you know about me, how would you set up Grok Bot?"→"set all those up for me" |
| 先问"人类同事能做好吗" | 指令连真人都看不懂，就先优化指令 |
| Grill Me 技能 | 让 Bot 反过来盘问你，了解你的业务 |
| 共享知识文件 | 跨 Bot 的事实写到 /workspace 里的文件 |
| 记日志 | 每次运行都记到 Sheet/ClickUp/数据库 |
| 外环/内环 | Bot 收集上下文、写 prompt，交给 cloud agent 去干 |
| 只分派的主管 | 在描述/记忆里写"永远不要自己干活" |
| 便携/可迁移 | 用 GitHub 备份 Bot 的配置和记忆，方便迁走 |
| 视觉区分 | 每个 Bot 配不同颜色/头像（v0.68.1 起聊天会染上 Bot 的颜色，**Settings → General → Accent: Black** 可以关掉） |
| 用证据说话 | 要求附上源链接、截图、时间戳和时区 |
| 别照抄别人的体系 | 把官方指南的链接发给幕僚长："analyze this link; before you create anything, give recommendations per our system" |
| 项目频道 | 每个项目开一个频道，把相关 Bot 拉进去，幕僚长在每个频道里都有；项目做完就归档 |

### 10.4 备份记忆（论坛员工给的完整提示词）
目前没有一键导出。员工建议直接发这段话（原文照录）：
> "Please make a full backup of your durable state. Make a folder backup/<your name>, copy your complete memory (profile + every memory-log entry, including older months) into memory.md, list your routines (schedule + prompt) in routines.md and your connectors in connectors.md, then zip the folder and send it here as an attachment."


### 10.5 上下文管理
- 每个 Bot 只有**一份对话和记忆**，一对一聊天和群聊共用，所以不同项目的内容会串在一起。建议：一个话题一个 Bot，用 **Hide from sidebar** 收起不用的，或者用 **Duplicate** 开一个干净的副本（副本不带记忆）。
- 共享浏览器会话：在一个 Bot 里登录/退出 Google，会影响所有 Bot（员工说按 Bot 隔离会话的功能在开发中，2026-10-02）。

### 10.6 管理员（Teams/Enterprise）
- 可以在 Cursor dashboard 的 Grok Bot 页面设置：Team Rules、Cloud Agent 委派、模板分享、本地执行上限、Network Controls、Team Setup、Team Secrets、是否允许本地出口转发、是否允许在 Bot 电脑上 sudo 等。环境是**每个用户一台持久的 Firecracker microVM**。
- 管理员**不能禁止**成员创建个人 Bot；能用的控制只有 Manage Group Access、Manage Team Bots（推给团队/分组）、按 Bot 设 Collaborators。
- 企业版有 Action Recording（审计记录，会打码卡号和 token，v0.58.0）。

---

## 11. 视频教程步骤汇总

> 说明：以下是各类公开教程里演示过的操作步骤，已去掉视频标题、频道与链接。界面以录制时为准；8–9 月的演示很多是旧界面，请对照第 2–9 节现行路径。步骤后的【→ x 节】表示对应本文功能章节。

### 11A. 完整演示步骤（按教程组）


#### 步骤组 1
- 要点与步骤：
 1. 概念：Grok Bot = Agent + 云端电脑（云端 VM，不在你的笔记本上）。点画面就能像远程桌面一样看它的终端、文件系统和 X。【→ 6 节】
 2. 教它做事的两种方式：录制（Teach a task），或者直接写 prompt。【→ 5.3】
 3. 演示：去 Costco/Amazon 比价买菜；遇到 2FA/登录就把电脑交给你。【→ 6.3】
 4. Marketplace Bot：在已经登录 Facebook 的浏览器里搜 Facebook Marketplace / Craigslist。
 5. 四大用途：写软件；把个人软件做成 Agent；跨 Slack/Notion/GitHub 检索信息；自动化流程（GTM 团队的 LinkedIn 外联、CRM、起草邮件）。
 6. DevRel Bot：每天读 X 书签 → 用他的风格写 prompt → 你批准/拒绝 → 通过 Cursor 集成启动 cloud agent → 发回截图/视频并开 PR。【→ 9 节】
 7. 支持 Cursor 支持的所有集成/MCP/插件和技能；可以连多个账户（多个 Gmail；Notion 的 SpaceX/Cursor/个人三个工作区）。⚠️ 帮助中心现在写 Gmail 一次只能连一个。【→ 3.3】
 8. 运行 Bot 的三种方式：聊天；例程（说"run on weekdays at 9am"或在 GUI 里设置时间表/Slack 消息/GitHub 事件）；被其他 Bot 触发（可组合）。【→ 4 节】
 9. 权限：（旧界面）profile → Settings → Bot settings → allow/deny 列表；审查代理检查每个动作（允许/拒绝/上报）。规则示例："发推之前先问我"。【→ 10.1】
 10. 一个 Bot 登录过的网站，其他 Bot 也都是登录状态（同一台电脑）。【→ 6.1】
 11. 群聊：别发"hey"让大家轮流回，用一个 Bot 来调度。【→ 8.2】
 12. 实战：用手机 10 分钟把粉丝所在地从 X 整理进 Notion 数据库；"Arnold" 健身 Bot 把训练记录写成 git commit 存进 JSON（"agents are software"）；外环 Agent（Lauren 的 pstack 插件）。
 13. 幕僚长/优先级 Bot：连 Notion、Gmail/日历草稿，做周复盘和周一启动。
- 备注：录制时的 Bot settings（name/title/description）界面后来已经改版（见 2.4）。

#### 步骤组 2
- **第 1 部分：Bot 是什么**
 1. Bot = 名字 + 职位（label）+ 描述；**描述很重要**：其他 Bot 靠描述决定委派给谁，可以搭成组织架构（你只跟几个"高管"说话）。
 2. 模板：打开模板链接 → 显示指令、记忆、技能 → Add bot；**个人连接（如 ClickUp）不会被带过去**。分享自己的 Bot：（旧界面）settings → "Share as template" → Publish。【→ 8.5】
 3. 记忆：全局记忆（所有 Bot 共享）和单个 Bot 的记忆；可以说"add this to your memory / to the global memory"；模板会带上记忆。
 4. 压缩（compaction）是自动的，用户不能控制。
- **第 2 部分：在哪里干活**
 5. 同一台电脑、每个 Bot 一块屏幕；浏览器登录会保存（你登一次，Bot 接着用）；文件区所有 Bot 共享；有终端。
 6. 本地文件：给它一个本机 Downloads 目录下的路径 → 它在你的电脑上分析、改名、移动文件。【→ 6.5】
- **第 3 部分：能力**
 7. 插件：Add → 登录；可以连多个账户（两个 ClickUp 账户，分别给不同的 Agent 用）。
 8. 技能：（当时）用 "/" 调用；技能位置在 Plugins → Installed → private plugins；好技能要写清楚什么时候用、准确顺序、出错怎么办。【→ 5.1】
 9. Teach a task：浏览器里点 "Teach a task" → Record → 显示 "<bot> is watching and learning" → 停止 → 生成技能。
 10. 例程："do this every day at 5 pm"，或者点 + 填名字、选技能、设间隔（演示用了每分钟，演示完记得关掉）。⚠️ 官方现在要求间隔至少 5 分钟。
 11. Webhook：表单应用 POST 数据过来 → Bot 给提交人发邮件，并在 ClickUp 里私信。【→ 4.5】
- **第 4 部分：多个 Bot 协作**：群聊（增删成员，比如"领导班子"群）、@ 提及（点一下就能跳到对应对话）、交接（可以查看 Bot 之间的对话）、回复按钮（隔离上下文）。【→ 8 节】
- **第 5 部分：控制与监控**
 12. 审批：（当时）Settings → Computer → "Execution on this computer"；可以选 always allow / allow once。
 13. 人工接管：不要把密码贴进聊天，万一贴了就去轮换。
 14. 每周额度每 7 天重置，可以按需付费；群聊更费额度。
 15. 运行记录（不能点进单次运行查看，失败的用 X 标出）。
 16. 每个 Bot 单独设置通知，和手机同步。
 17. 右键菜单：置顶、移到分组、隐藏、删除、分享模板、编辑资料、标为未读、复制（**清空对话历史但保留技能/例程**）、复制对话 ID（给客服用）。
 18. Agent 日志：所有事都记到 Sheet/ClickUp/数据库。

#### 步骤组 3
 1. **Grill Me 技能**：把技能文件拖进聊天 → "read this skill, make a new skill … use this on me to learn about my business" → 保存 → 以后说"grill me about X"。【→ 5.4】
 2. **幕僚长 Klaus（置顶）的描述原文**："This is Nate's chief of staff. Klaus is the only bot Nate talks to before doing any task. Check whether another Grok Bot owns it and delegate first. Only do the work yourself if no specialist fits. Bring the results back here." 专员 Bot 的描述也要写清楚。【→ 8.3】
 3. 问幕僚长"还应该加哪些子代理"。
 4. 记忆分为单个 Bot 的和全体共享的，要说清楚哪些共享。附加技巧：消息右侧三点 → Start a thread。
 5. 用 Composio 插件连 YouTube、Reddit、LinkedIn、Perplexity 等原生不支持的服务，并记到共享知识里。【→ 3.8】
 6. Agent 日志技能："log grok bot work to ClickUp"。
 7. Teach a task：电脑画面 → Teach a task → 录制 → 停止 → "learn from demonstration" → 生成技能。
 8. 例程：（旧界面）右侧面板 → Create routine → 名字、指令（可以写"run this skill"或"talk to these agents"）→ 触发方式：定时（每小时/每天/间隔）或事件（Slack 消息、Git 事件、Teams 消息）；设备都关机了也会在云端跑。
 9. Agent 电脑保存了登录状态（例如 Skool），不用再贴凭据。

#### 步骤组 4
- 视频里念的官方团队（Ben Lang 等）技巧清单：
 - 让 Grok Bot 在你的 Mac 上装 Peekaboo，获得更多类似 OpenClaw 的能力
 - 按角色建 Bot（设计师、工程师、PM），每个有自己的系统提示和例程
 - 用频道/群聊管理项目，同一个 Bot 可以进多个频道
 - 例程别太频繁，"never run a routine every 5 minutes"
 - 周期性任务交给一个新 Bot，你继续和主 Bot 聊
 - 用规则控制允许/拒绝，连了 X/Gmail 的 Bot 要拦住写操作和破坏性操作
 - 给它看你以前亲手写的内容作为范例
 - 建一个"chief of agents"：定好所有 Bot 都要遵守的规则（只起草、命名、语气、允许的工具），并让它用同样的规则建新 Bot
 - 例程先问"真人同事能做好吗"
 - 名字会影响行为（作者说：名字只是标签，**真正引导行为的是描述，规则要写在描述里**）。
- 演示步骤：
 1. 入门页 "What do you use every day?" → Next。
 2. 新建幕僚长 Bot "Elon" 并置顶。
 3. 告诉它目标（10 万 X 粉丝）→ 它自己建了 Research / Copy / Studio 等 Bot。【→ 8.3】
 4. X 连接器授权后，API 调用需要买 credits → 作者改为在 VM 的 Chrome 里登录 X，并断开 X API 连接器（否则会默认走 API，把免费额度用光）。⚠️ 10-07 起已有免费的内置 X 读取（见 3.5）。
 5. 建分组（改名为 "X"）并把 Bot 拖进去。
 6. 从 YouTube 字幕提取自己的"语气 DNA"。
 7. Bot 发的帖子在 X 上会标 "Made with AI" → 改为让 Bot 存草稿，由真人发布。
 8. 每日例程：X 趋势 × Google Trends × YouTube → 5 个视频选题。
 9. Inbox Bot + Gmail 插件（Plugins → Add Gmail → 授权）；再加 partnerships、leads 两个 Bot。
 10. 手动建频道 "Gmail workspace"（partnerships、inbox、Elon、leads）；分组要手动建，把 Bot 拖进去。
 11. 在频道里定规则："do not send anything on my behalf yet"。
 12. 审批：本地操作用 "allow once"；有审批等待时会显示橙色标记。
 13. 工程分组：默认的 Grok Bot + Codex Bot + Claude Bot，分别调用装好的 Codex/Claude CLI（在 VM 上，或者通过 Mac/Peekaboo），再回来汇报；连上 GitHub。
 14. 整套搭下来用掉了 SuperGrok Heavy 每周额度的 7%。

#### 步骤组 5
- ⚠️ 作者说"每个 Bot 有自己的电脑"，和官方 docs（一台共享电脑、多块屏幕）不一致。
 1. 第一个 Bot 做 CEO/幕僚长：右键 → Pin；（旧界面）Edit profile → title "Chief of Staff"（显示在名字旁边）；描述 = 每次提问都会带上的上下文（相当于系统提示）。
 2. 作者习惯用真人名字 + 职位来命名。
 3. **Brain dump → 反向提问**：先把自己的一切告诉它，然后问"Based on what you know about me, how would you set up Grok Bot? Which bots, roles, responsibilities, routines?"→ 再说"That's great, set all those up for me"，它会自己建 Bot。【→ 2.6】
 4. 插件（左下角）：Gmail、Google Calendar、X；用第三方 AgentMail 给 Bot 单独开邮箱（**现已有官方 Bot 邮箱**）；写代码的人可以装 Vercel 插件；技能也放在插件里；推荐 Matt Van Horn 的 "last30days" 技能。
 5. 例程 = cron 任务；同样可以反向提问："what are the best routines we can set up?"
 6. 他的阵容：Build（网管/程序员，通过 Tailscale 访问他所有设备，在 5090 上跑本地模型）、Barry（内容，用 X API，7:00–23:30 每 30 分钟查 AI 实验室账号）、Dusty（用自己的邮箱账号管理 Skool 社区）、Cindy（营收运营，赞助邮件分拣进表格）、Reed（实验员，归 CEO Slate 管）。
 7. 观点：每个 Bot 的上下文越小，就越快、越便宜、越聪明；Hermes 留给自定义模型/本地管理，Grok Bot 用于知识型工作。

#### 步骤组 6
 1. 去 x.ai/bot 下载 App，看价格页（不免费）。⚠️ 作者说自己用的是"Grok Plus plan $100/月"，这个名字和官方套餐名对不上。
 2. 第一个 Bot = 行政助理：点 + → Create new bot → 点名字改成 "executive assistant" → 简单写个描述 → 保持通知开启。
 3. Bot 会问它负责什么 → 回答"day-to-day ops"、"You are my executive assistant…"，之后它会慢慢学习。
 4. 左下角点你的名字 → Settings：Usage & Billing（用到 100% 就不能用了，要等重置或付费）；更新 App 和 Bot 的电脑；卡住时重置电脑（不常用）。
 5. 连账户：在聊天里说"connect my email accounts. Let's start with Gmail"→"add another Gmail"（标签 work）→ 登录；也可以通过 Composio/Zapier 这类 MCP 聚合器。
 6. 左侧是 Bot 列表；点屏幕图标看 Bot 的桌面，在 Chrome 里登录 gmail.com 等网站。
 7. Teach a task：右上角按钮 → 你操作，它在旁边看。
 8. 测试："find me the three best sound bars under $500 on Amazon. Do this on your computer so I can see it."
 9. 幕僚长 + 专员 Bot（Researcher 1…），Agent 之间会自己发消息。
 10. 例程："Every morning at 9:00 a.m., look through my Slack and email and send me a report."；问行政助理"what routines should you have?"
 11. 商业 Bot：财务（QuickBooks、催发票）、Newsletter（Beehiiv）、内容策略、LinkedIn 外联、调研。
 12. 市场：导入 "Doctor Eggbot" → Import bot；有精选插件，View all 里有 Slack、Notion、密码管理器。
 13. 用 + 建群：行政助理 + 财务 + COO，问"Do we have any outstanding invoices?"（主要是为了看到全局）。
 14. 邮件："Any emails I should respond to? Brief me."→"Reply to Lisa saying thanks"。
- 作者提醒：所有 Bot 共用一台云电脑（文件、浏览器、登录），要明确告诉某些 Bot 不要碰某些账户；替你和客服聊天时比真人慢。

#### 步骤组 7
 1. 左上 **+** → 两个选项：建立 Bot / 建立群组 → 选建立 Bot → 选角色（个人助理 / 财务与行政 / 研究与数据 / 知识… 或者自己输入）→ 选"研究与数据"，大约 1 分钟建好。【→ 2.4】
 2. "调查大佬"公式：一个 Bot + 一份 brief + 一次人工核对。对比：只说"帮我找最近的 AI 新闻"→ 结果很宽泛；用四行 brief → 得到可以直接用的清单（见 2.5）。可以让 Bot 在电脑上打开链接核对，右侧能看到浏览器。
 3. 一句话设例程："每个工作日早上九点（香港时间）按刚才的 brief 做一次调查，结果放在这个对话里，不要发邮件；找不到就写找不到"→ Bot 自动建例程（自己选了工作日 8:50，好让你 9 点前收到），并把 brief 改写成更细的英文指令（**建议检查一下有没有漏掉你的规则**）；面板上可以暂停、看运行记录；可以马上试跑一次。【→ 4.2】
 4. 结果：10/5（周一）8:55 自动交了报告；到 10/3 连续 13 天都按时交。第一周每天点开一条链接核对；没交的话先看运行记录；你太久不回应，例程会被暂停。
 5. 本机权限：每次询问（默认）/ 总是允许 / 永不允许；官方建议不需要本机文件就选"永不允许"。
 6. 价格：作者走 SuperGrok 路线，优惠价 9.9 美元（原价 30 美元/月，作者口述）；"SuperGrok 连了 Cursor 账户就改不回来"（和官方说的"链接永久"一致）。作者还说官方支持地区不包括香港、账户有风控风险（未证实说法）。

#### 步骤组 8
 1. 语音是在桌面和手机上分几天陆续开放的（他手机上一开始没有）。
 2. 输入框右下角的语音图标 → 点击；有静音按钮；要分别和每个 Bot 通话。
 3. 通话设置：Voice（每个 Bot 可以用不同声音）、Speed（默认偏慢，他用 1.25–1.5 倍）、Language（十几种可选或 Auto-detect）。
 4. 演示：用语音让主调度 Bot 把邮件分拣交给邮件专员 → 邮件 Bot 起草跟进邮件；用语音让它操作电脑：打开 Instagram 登录页，它拒绝替你输入登录信息（要你自己登）。
 5. 没有原生插件的应用（Drip）可以通过 Zapier MCP 用语音操作；但它不听"stop talking"的指令。作者认为 ChatGPT 的语音模式更好。【→ 7.1】

#### 步骤组 9
 1. **主 Bot**（星标）：自动委派给其他 Bot；任何 Bot 都能成为主 Bot："Hey, can you now become the primary bot?"；把主 Bot 置顶；开发 Bot 会用 Cursor（他在 prompt 里写了"Please use Opus 5.5"，⚠️ 官方说 Bot 本身不能指定模型）；详情面板的 "Coding" 标签可以打开 Cursor 看代理的工作。技巧：散步时用 iOS 语音按钮给主 Bot 打电话，通话结束后会生成一张带完整文字记录的卡片。【→ 8.3】
 2. **Team Bots**：New → Team bots → Create new team bot；设置面板有 Plugins / Secrets / Skills / Files；新建时是干净的（个人 Bot 会共享你全部的插件和技能，个人 Bot 之间只有系统提示和对话上下文不同）；**Publish to team** 之前是草稿；详情面板往下拉 → "Bring to your team's Slack" → 选工作区 → connect → Done；把它加进 Slack 频道成员，然后 @它；Gmail 这类登录型插件按每个同事分别连接（用提问的那个同事的账户）；例程留在个人那边。【→ 8.4】
 3. **内联草稿**：邮件/Slack 草稿以"迷你应用"卡片显示，可以编辑、Send、Discard；"draft up an email to the top 10 emails I need to respond to"→ 生成 10 张卡片。【→ 7.3】
 4. **Settings → Computer → Network → "Route traffic through this computer"**：用你的 IP 出网，让 Target 这类网站不容易拦截云端 VM。【→ 6.6】
 - 附加：用 Composio 当跨平台的插件/技能中心。

#### 步骤组 10
- **09-30**
 - Team Bots：共享插件、资料、流程、文档，例如客服流程。
 - 通过 Plaid 连接银行/证券账户："how much did I spend last month"，找出没在用的订阅并帮你退订。
 - 语音：可以直接开口打断；通话中会搜你的其他消息；掉线会重连。
- **10-13**
 - Bot 邮箱："I hear you have some email inboxes. Can you give me an email handle?"，可以用来注册、订阅 newsletter、购物（"好名字抢得很快"）。
 - 不连 X 账户也能免费读和监控 X。
 - Shopify。
 - 每个 Bot 有自己的颜色。
 - 邮件草稿卡片可以预览格式，多个别名时有发件人下拉框。
 - 电脑更快、分辨率更高。
 - 在聊天里搜模板会显示模板卡片。
 - PR 悬停预览。
 - 例程右键菜单。
 - 新增 5 种语言（Marathi、Tamil、Telugu、Swahili、Hausa）。
- **10-18**（⚠️ 截至 10-20 changelog 里还没有）
 - 聊天卡片改版（Markdown 表格、ABC 多选题）。
 - Bot 电脑再次升级，"响应快 3 倍"。
 - Bot 改 Slack/Gmail 草稿时，改动处会高亮。
 - plus 地址别名（"Hey, can you make a plus address email for me"），相当于不限数量的地址。
 - Bot 可以读并回复 X Chat 私信（有限制，禁止刷屏）。
 - 在 X 线程里 需要装好并配置 X 插件。
 - 新连接器：Robinhood、Coinbase、Trello 等。
 - 挂断后 Bot 在 30 分钟内还记得通话内容。
 - 通知只发到你正在用的设备。
 - 可以邀请同事（团队/企业）。
 - 可以把 Grok Bot 设为 Android 默认助手。
 - Team Bot 访问列表。
 - 手机上有未读提示。
 - 例程时间改进。
 - Slack 登录链接改进。

#### 步骤组 11
 1. **入门**：在一个聊天里跨工具检索信息（changelog、文档、X/LinkedIn 文案、字幕）；一个插件可以连多个账户（Notion、多个 Slack）。
 2. 长期 Bot 和一次性 Bot 分开；置顶"核心六个"（健身 Franco、管 Slack 的行政助理、管邮件/日历的幕僚长……）；写作 Bot 由其他 Bot 用 @ 调用。
 3. **中级**：Bot 可以组合——一个 Bot 可以调用或创建另一个（" rewrite this"）；用 "new channel" 图标建群（当时叫 channels）：写作 Bot + 产品专家 + X 算法 Bot。多建专员，少建大而全的 Bot。
 4. **高级**：X → Notion 个人 CRM（口述 prompt，大约 30 分钟搭好）；每日例程：挑出 90 天没联系的 10 个人 + 开场白；例程可以设"run every 4 hours"。
 5. **God mode**：技术 demo Bot 读 X 书签 → 问"make a demo?"→ 启动 Cursor cloud agent → 构建、验证、截图 → Cloudflare 预览部署 → 在 Cursor App 里打开。"Loops" Bot = 外环，负责合成 prompt，交给 Cursor 内环执行。【→ 9 节】

#### 步骤组 12
 1. 引导页 → Next；选头像颜色（红）和形状（三角）→ Get started。
 2. 第一个 prompt："Go to Product Hunt and find the top five AI tools launched this week. Give me a list with one line on what each does." → 看着它操作电脑（鼠标在动）。
 3. 给 Bot 改名。
 4. Teach a task：点 "Teach task" → 开始录制 → 访问 Hacker News（出现 CAPTCHA，他自己解了）、OpenAI News、xAI News → 暂停/停止 → 加一句 prompt："Save that as a routine. Every morning at 8:00 a.m., check Hacker News, OpenAI blog, xAI news for new AI tools/models. Message me anything a beginner coding audience would care about with one line of why it matters."【→ 5.3】
 5. （旧界面）例程出现在右侧面板，有指令、时间表、历史记录，可以开关；Test run 会真的执行；演示中浏览器崩了一下，随后恢复。
 6. 触发方式：Slack 消息、Git 事件、Teams 消息、cron、webhook。
 7. Cursor 里装过的东西都会同步到 Grok Bot。
 8. 新建 Bot 并建一个频道（群），让 Bot 共享上下文；思路是"想象把 AI Agent 换成一个真人"。

#### 步骤组 13
 1. **ChatGPT / Claude Code 桥接**：对幕僚长说"create a new bot for me to communicate with Grok Bot from ChatGPT/Claude Code … routine triggered by webhook … and a skill"→ 得到一个"bridge bot"，带例程 "inbound command webhook"（Add another → 选触发类型；webhook 会给出凭据）→ 在 ChatGPT 里建一个用这些凭据 POST 指令的技能 → 测试。例如发"Create a new routine for my time tracker bot, end of day recap at 6:00"→ 桥接 Bot 把消息转给 time-tracker Bot，由它给自己建例程。【→ 4.5】
 2. 插件：Marketplace → plugins；Gmail 可以连多个账户（⚠️ 现在帮助中心说一次只能一个）；用 Composio 当 MCP 枢纽；在 Grok Bot 里直接加自定义 MCP："find the custom MCP server of Buffer and add it"→ 它要 API key 时说"do the OAuth path"→ Authorize。【→ 3.8】
 3. Bot 市场（Marketplace → Bots）：市场上的 "Overheard"（汇总 Reddit/HN/新闻/X 上的提及，工作日发摘要）、"Dr. Eggbot"（帮你设计 Bot）→ Import bot → Bot 自己跑设置流程（问你信息、请你连应用、建例程）。分享模板 → publish → 链接（上架市场可能要审核，作者也不确定）。
 4. 电脑访问：（当时）Profile → Settings → Computer → 本地执行设为 Always allow / Ask every time；"tell me what's in my Downloads"；用手机控制 Mac（看屏幕截图）；手机上的电脑画面有触控板模式。
 5. 整理：右键 → Pin；Move to → 新分组；拖动排序。
 6. 项目频道：群 = 频道；幕僚长 + 项目经理 + Notion 项目/任务数据库。
 7. （当时）用 AgentMail 给 Bot 开邮箱，并且让 Bot 自己去注册 AgentMail 账号（"are there other ways you can do this without involving me?"）。
 8. `Cmd+K` 搜索 Bot、例程、技能；`Cmd+方向键` 切换标签；`Cmd+数字` 打开对应 Bot；Actions 标签。
 9. **省 token**：先用 Teach a task 生成技能 → 再说"turn this browser-use skill into a script to make it run more efficiently"（它找到了用 Apify 的办法）。

#### 步骤组 14
 1. **模型路由**（Elon 在 X 上宣布）：按任务选最佳后端模型，包括 Claude Opus 5.5、Midjourney、Suno 等 API；简单任务用小而快的模型；"大部分 Bot 请求……等 Grok 4.8 推出后用它的极速版"。用户看不到、也控制不了模型和推理强度，有人希望能关掉。【→ 1.4】
 2. **内置的免费 X 搜索/阅读/监控**：他让 X Bot 通知其他所有 Bot 把例程改成用原生 X；Bot 说每天有 1,000 次免费读取（约美中时间 19 点重置），**作者自己也说别全信**。【→ 3.5】
 3. 建议（推测性）：把大技能拆成小技能，路由就能在每一步选更便宜的模型。
 4. 技巧：在 Bot 电脑终端里装 Claude Code / Codex CLI → Take over 用自己的订阅登录 → clone 仓库；建名叫 "Claude Code"/"Codex" 的 Bot；用 API key 和可迁移的文件方便以后迁移。【→ 6.4】

#### 步骤组 15
 1. 组织结构：Chief Bot（主入口）→ 项目负责人（按项目分，不按任务分）→ 共享专员（比如多个项目共用的文案）。
 2. 置顶 Chief 和正在进行的项目负责人；一次性的/专员 Bot 用 Hide from sidebar 收起来。
 3. 用默认的颜色和形状（保留动画）：只有 Chief 用紫色 + 云朵形；负责人各用不同颜色 + 三角形；专员一律绿色方块。
 4. 两种频道："All hands"（Chief + 负责人，每个工作日在他起床前开会协调，避免日程冲突）和项目频道（负责人 + 专员）；项目频道数量不超过三角形 Bot 的数量。
 5. 模板就像技能文件，只当灵感参考。
 6. "Overwatch" Bot：负责整理、备份到 GitHub、保持可迁移（万一额度用完或者离开 Grok Bot）；用例程定期清理、整理、备份。【→ 10.3】

#### 步骤组 16
 1. **Bot 之间私信导致 token 浪费**：每条私信都会唤醒对方的上下文，幕僚长和每个被委派的 Bot 的上下文都要计费。对策：没办法直接清空 Bot 的上下文 → 给那个 Bot 单开一个频道，太长了就新开一个（删掉/归档旧的）；定一条规则"Bot 只在频道里交流，不要频繁私信"；要求管理者 Bot 注意省 token。
 2. **违反服务条款/封号风险**：浏览器登录 + 抓取看起来像自动化采集。对策：用官方 API/OAuth 连接器，最小权限（只读），禁止机器人的网站别用主账号，重要账号远离实验。
 3. **共享登录**：在共享电脑上，每个平台只能保持一个登录，登第二个账户会顶掉第一个；没有直接的解决办法 → 只保留"全团队都能碰也没关系"的登录。【→ 6.1】

#### 步骤组 17
 1. 去官网下载对应系统的版本 → 登录账号就能用；界面像公司聊天软件（左侧是联系人，可以建群组）。
 2. 三个特别之处：多个职位的 Bot 拉进同一个群；每个 Bot 长期负责一项工作并保留记忆；派活后可以关电脑，云端继续干，完成或需要确认时通知手机。
 3. 建 Bot 方法一：左上角 **+** → 建立新 Bot；方法二：在"市集"(Marketplace) 安装 eggbot（专门帮你建 Bot 的 Bot）。
 4. 用大白话告诉 eggbot：建一个能分析 YouTube 链接、提取文字稿、整理成带截图的图文学习笔记的 Bot → 它追问语言、交付形式、语气 → 生成"YouTube 笔记师"Bot → 丢链接进去 → 产出带时间戳的操作文档。
 5. 建"总经理"Bot，关键一句是"**检查交付物，再要求修改**"，否则它只会转发。
 6. X 账号运营团队：从第三方网站复制别人分享的团队提示词（不是一键导入）丢给 eggbot → 自动建好专员 + 总经理 + 群聊"X 运营 5 人队"→ 它主动要资料（细分领域、竞品、你最满意的 20 篇帖子、X 账号）→ 自己试跑：创意员出 5 个选题 → 分析师筛出 3 个 → 规划员排 3 天日历。
 7. 第一版不满意 → 只对总经理提 3 点修改（选题热度、分析师的筛选标准、文案更口语化）→ 总经理自己去调整三个员工的设定。
 8. 发布：在这个 Bot 的云电脑上登录自己的 X → Bot 发推；排程："下一则推文今天北京时间 12 点发"。⚠️ 用浏览器自动发帖有被冻结账号的先例（见 12 节）。
 9. 数据看板：eggbot 建一个"X 看板工程师"拉进群 → 生成粉丝/浏览/评论卡片 + 本周发布计划 + 热门帖子的网页。
 - 作者评价：设计能力不如 Codex，可以搭配使用。

#### 步骤组 18
 1. 逐篇试了官方指南（x.ai/bot/guides，截至 08-31）：
 - 多团队管理（项目频道 + Notion 项目/任务库 + projects manager）
 - 手机游戏工作室（6 个 Bot：移动端调度、数据分析、素材、工程、GCS、bug 修复，提示词可以直接复制）
 - 设计团队（Experiments、Motion God、Figma Bro、Devbot）
 - 企业 GTM（连 Salesforce、Granola、Drive、Gmail、Calendar；让 Bot 扫 Gmail 和 Slack 学你的写作风格；幕僚长；每日会前准备；每周媒体汇总）
 - PM（关注清单、团队幕僚长 "Emily"、5 个工程 Bot 各开 cloud agent）。
 官方指南入口：
 2. 技巧：把指南链接贴给幕僚长："analyze this link; before you create anything, give recommendations per our system"，不要照搬别人的体系。
 3. 每个项目建一个频道，拉相关 Bot，幕僚长在每个团队频道里都在（掌握上下文）；做完的项目归档；可以让 Bot 帮你建项目频道（"create a new project channel… test project"）。
 4. 幕僚长建的设计团队会自动拉取品牌色值和字体。
 5. 本地命令的审批会出现在对应的那个 Bot 里。

#### 步骤组 19
- 开场对比：Hermes/OpenClaw 要用终端/Python、自己托管，开源免费但要付模型费；Grok Bot 下载就能用、云端托管、要付订阅（"散件 vs 整机"）。
- **L1 消息**：侧边栏是有持久对话的 Bot（像 Slack）；健康 Agent 会主动提醒你补蛋白质；Bot 之间的消息显示成蓝色矩形，可以点开看。
- **L2 工具/插件**：左下 Plugins 按钮 → 顶部显示已连接数量（作者有 17 个）→ Featured / View all / 搜索 → Add → 显示 "Needs authentication" → Authenticate → 浏览器授权 → 完成（以 Google Drive 为例）。
 - 外部 MCP：在 Higgsfield 网站选 Grok Bot → 点 "add Higgsfield plugin" 按钮，会唤起 Grok Bot 卡片 → Add（89 个工具）。也可以直接对 Bot 说"go and add ClickUp for me"。
 - 建 Bot：左上 + → Create new bot → brain dump 职责 → Bot 自己写指令（⚠️ 他演示的查看路径"右上 computer 图标 → Settings（Name/Description）"在 10 月界面中已经不存在了）。
 - IG 轮播技能：上传参考图锁定风格 → 喂 YouTube 链接，自动抓字幕 → 5–6 分钟生成轮播图 → 连 Blotato MCP（Blotato Settings → API → "other MCP clients" 复制配置 → 贴给 Bot："connect the … MCP using this"，30 秒配好）→ "schedule tomorrow 5pm PT"；还能配置评论关键词自动私信。
 - Firecrawl 插件：把网页抓成干净格式，省 token（作者说有免费 1,000 credits）；用例：deal scout 价格监控。
- **L3 任务管理**：ClickUp 插件（58 个工具）；建一个 "agent tracker" Bot：To do / In progress / Done 三列，截止日期写进 due date；tracker 给其他 Bot 定一条 standing rule："开始、完成、卡住时都 ping 我"。
 - 会议 Agent：Granola 插件；brain dump 需求 → Bot 自己配好例程 "Granola action item sweep"（工作日 10/13/16/19 点，每 3 小时一次）→ 把待办分给各个 Bot、更新 ClickUp、只有变化时才发摘要。
- **L4 例程**：（旧界面）点 Bot → 右上 computer 图标旁的例程列表 → 点开可以看指令、改频率、加新例程、设事件触发（如 Slack 新消息）；黑色的 Test run 按钮可以马上运行。
- **L5 记忆与技能**：越用越懂你；"turn it into a skill"；需要积累几天。
 - Zapier MCP：Zapier → Create → MCP servers → 选 Cursor → 复制 prompt → 贴给主 Bot → 在卡片上确认连接 → 回 Zapier 里 Add apps（如 Drip 的 23 个 actions）。
- **L6 模板**：（旧界面）右上下载图标 → Share as a template → Bot 收到 "Create a template of yourself…" → 模板里有 skills、routines、上下文/指令、files、integrations（⚠️ 作者说会带上 Higgsfield 凭证，分享前要检查敏感信息）→ Publish → Copy link。
- **L7 Bot 团队**：主 Agent → 操作员/专员；新建一个群聊，把两个 Bot 介绍给对方，让它们自己商量分工（IG 轮播 vs 短视频）。

#### 步骤组 20
- 作者提醒：
 - 很烧 token（有人一两天就见底）
 - beta 偶尔会卡
 - 套餐有坑：作者用公司的 SuperGrok 企业版付费后没法用，当时（2026-09）**企业版/团队版 SuperGrok 不支持**，只支持个人版；建议没有 Grok 账号的人直接用 Cursor 付费方案（⚠️ 10-12 之后请看 2.1 节的 X Subscription）。
- 步骤：
 1. 官网下载桌面版 → 安装打开 → 三个选项：免费试用 / **Get Pro** / **Get Access with Grok**（需要个人版 SuperGrok、Plus、Heavy 或 X Premium+）。
 2. Get Pro → 付款 → 确认页会显示你的 Cursor 账号 → 在 App 弹出的登录页 Sign in。
 3. 第一次使用的介绍 + 勾选常用工具（Google、Slack、Notion…）。
 4. 建第一个 Bot：一个 Bot 一项职责；名字用具体职称（信箱助理 / Google Ads 数据巡查 / SEO 排名监测 / 社群留言整理）。
 5. 直接口头交代："你是我的信箱助理，每天早上帮我看收件匣，把重要、需要我回的信挑出来整理给我"→ Bot 会反问你 → 提议连 Gmail 并设成平日早上 9 点自动整理 → 点"好，连 Gmail"→ 授权 → 拿到 31 个 Gmail 工具（所有 Bot 共用）→ 右侧自动建好例程"平日早上信箱摘要"。
 6. 说"现在就先扫一轮"→ 分成三类（需要留意 / 财务营运知悉 / 被 CC 的项目进度）→ Bot 问你要不要拟草稿或打标记，等你确认才动手。
- 说明：作者说"示范一次就存成 routine"，但官方 docs 说 Teach a task 生成的是 skill（见 5.3）。作者团队自己做了一个 Google Search Console MCP，说放在他们的 GitHub 上（链接未核实，本文不列出）。

#### 步骤组 21
 1. **X Subscription**：Grok + Cursor + X Premium 合成一个订阅，5 档从 Free 到 Ultra（$200/月）；可选加入；Grok/Composer 的编码额度至少是对应 Cursor 方案的 2 倍，Ultra 达到 50 倍以上（作者口述）；但第三方模型按 API 列表价扣 credits → 重度使用第三方模型的人可以考虑留在旧套餐；入口 cursor.com/unify。【→ 2.1】
 2. Elon：Grok Bot 会为每个任务选最佳后端模型。Bot 自己也说看不到、无法告诉你用的是哪个模型。
 3. **Bot 邮箱**：把官方推文贴给 Bot 说"Go set it up"→ 它问 @ 前面用什么 → 认领 moritz@…；也可以在 X 上 让它设置；Connect apps → connected apps → Show all plugins 里能看到邮箱连接；只能有一个邮箱。⚠️ 作者说"想换名要放弃现有的"，但帮助中心说地址**不能改名也不能删除**。【→ 3.6】
 4. **原生 X 搜索**：不用设置、免费、只看公开信息；私信、书签、首页时间线仍然需要 X API（付费，作者说首次连接会送 $99 credits）。坑：他的摘要例程读首页时间线时走了 API，烧掉了 credits → 让 Bot 改成只用公开数据检查他关注的几个账号。【→ 3.5】
 5. **Cursor 集成**：Bot 把编码交给 Cursor，用 GitHub/Origin 插件管理 PR；Bot 可以给 Cursor "project"（云端，一个线程协调多个子代理）发消息，触发云端/本地代理；"合并前先问我"；给出截图 + Vercel 预览；"Can you do a video demo of what you just built?"【→ 9 节】
- 用例：
 - 合作 Bot：每天扫赞助邮件，按规则起草回复，发送前审批；渠道包括 email / Passion Fruit / X 私信。
 - 每周 SEO 检查例程。
 - 交易 Bot：Alpaca 模拟交易，凭据放在 1Password 里共享给 Bot。

#### 步骤组 22
- ⚠️ 作者说"现在默认模型是 Opus"（以前是 Grok 4.7），官方没有这样说过（见 1.4）。
 1. **在 X 回复里 **： 会回"I'm sending this to your Grok Bot"→ 主 Bot 被 " mentioned on X" 触发，在 App 里给你详细回复（回复不公开）→ 之后可以追加到你的 taste profile markdown 里。【→ 7.4】
 2. **免费 X 数据**（作者说每天 1,000 次拉取、每次最多 25 条，**未经官方证实**）。用例：
 - 品牌情绪分析 + 交互式对比网站（10–15 分钟做完）
 - 分析自己帖子的表现（按格式、月份、主题）
 - YouTube 选题（X × YouTube 话题矩阵）
 - 私信分析：作者测试时私信内容是加密的，只能看到发件人。
 3. **Bot 邮箱**：建议用助理的名字；把 Bot 邮箱加进邮件往来 → 它查日历给出空档并起草回复 → 你确认后发送 → "yes, add the invite"。主动报告：业务周报（YouTube、Mercury 银行、网站/课程数据）、周末活动报告（每周五早 7 点的例程）、健康报告；回复报告邮件，让 Bot 去 Linear 起草 ticket。
 4. 把多个 Bot 放进同一个线程，讨论合并重复的报告。
 5. 用 Suno 生成音乐、做 rap battle 视频。
 6. Slack 社群客服 Bot：每天扫一遍支持请求（退款、争议、邮箱不一致），起草回复，等你确认。
 - 角色化 Bot：YouTube 制作人、"loving husband"、周末规划师、健康教练等。

#### 步骤组 23
 1. Peng 的整理方式：按工作/生活分组，置顶最常用的（Chief of Staff / Designer / Writer）；不知道交给谁，就发给 Chief of Staff。
 2. 生活用例：
 - 买 3D 打印耗材并更新 Notion 库存
 - 一个 prompt 搞定 FB Marketplace 卖二手：查官方价 → 比本地价 → 咨询 Writer Bot 学过往写法 → 发帖 → 没人问就每周降 5 美元
 - 截图就能让 Bot 建/改日程；需要登录时弹出 sign-in，由你 Take over。
 3. Writer Bot 帮你润色英文邮件/Slack，草稿以内联卡片审阅后发送。
 4. 群聊：把 PM、设计等 3 个 Bot 拉进一个群讨论想法；@ 谁只有谁回；不 @ 时由 Bot 自己判断要不要回。**同一个 Bot 在不同群/项目里是同一个实体**（共享上下文）。理念："Bot 是持久的角色，有身份、记忆和工具；项目用单独的会话/群聊"。
 5. Podcast Bot：发文章链接就转成播客音频。个人网站打卡流水线：Bot 去私有仓库拉最新的 playbook，按步骤生成图片并发布。
 6. Designer Bot：通过 Figma MCP 连接器直接改 frame，用技能记住设计系统；"我只做前 5%：搭好系统 + 一个关键帧，让它扩展成完整流程"。
 7. Lauren（工程）：用本地执行守护进程让 Bot 控制她电脑上的 Omarchy VM 并自我验证；"Crumb" Bot 负责清理磁盘；PStack 插件用 Cursor cloud agents 跑 eval（用不同模型的子代理），达标才合并。
 8. **Dr. Eggbot**：设计 Bot 的 Bot；有个例程定期审阅所有 Bot 和对话记录，提出技能、新 Bot、例程方面的调整，并提醒"例程太频繁很费额度"（比如每 10 分钟唤醒一次）。
 9. 工程组织：Chief of Staff → Eng lead（描述里写明"永远不要自己干活，总是委派给这几个 Bot"，附 Bot ID）→ 工程 Bot → 每个都开 cloud agent（可以按任务选不同模型/推理强度）。大项目先在 Notion 文档里分阶段规划再委派。部分 PR 自动合并（代码库专门为 Agent 设计）——"米其林厨房"式的规模化质量。
 10. 个人助理 Suki：通过 Navan 订复杂的多段机票 + 酒店，下单前先确认；用第三方服务给 Bot 一个电话号码，发短信提醒吃午饭；DoorDash 点餐；Amazon 复购。
 11. 社交 Bot "Potato"：每 30 分钟用例程检查 X 提及，汇总 bug 报告 → 作为外环喂给工程 Bot（"go talk to potato"）；技能 "/potato-mode"。
 12. Cursor 和 Grok Bot 的插件是通用的（同一个 PStack 两边都能装）；插件可以同时包含 MCP 和 skills（X 插件里就有一个 skill）。
 13. **推荐路线**：先手动做 → 观察并纠偏 → 做成技能（例如 /expense-report 能一次成功）→ 再设例程；"信任是一点点建立的"。能力（工具/连接器/技能）共享，记忆和偏好属于各自的 Bot；先做简单任务 → 周期任务 → 例程 → 太耗 token 就降频 → 再接更多工具。


### 11B. 其他教程章节要点（无完整逐步内容）

> 下列条目只根据公开视频的章节标题或简介整理。已去掉标题出处、作者与链接；保留章节里的操作要点。


**要点 1**
- 0:00 Intro → 0:40 Get past sites that block your bot → 1:45 Trick 1: House rules that block outside orders → 3:10 Email injection test → 5:55 Trick 2: Auto-review and custom rules → 7:15 When rules conflict, Ask first wins → 9:20 Trick 3: Connect GrokBot to your PC → 11:50 Tailscale, the permanent link → 14:00 Trick 4: Run a local model on your PC → 17:05 Trick 5: Claude Code as the default coder → 19:25 Which trick to set up first → 20:05 Bonus: talk to GrokBot from your iPhone action button → 21:20 Outro → 22:00 Blessing

**要点 2**
- 0:00 Intro → 0:41 Rakaza + Model Breakdown → 5:12 Installation Guide → 14:33 Rakaza Setup & Walkthrough

**要点 3**
- 0:00 14 Grok Bot employees you can steal → 0:29 The software they replace → 1:09 Why Grok Bot over OpenClaw and Hermes → 1:20 They work while you sleep → 1:40 How you talk to your employees → 1:54 Employee 1: Chief of Staff → 4:58 Employee 2: Dr. Eggbot, the bot that builds bots → 7:10 Employee 3: Matcha, your project manager → 7:57 Employee 4: Potato runs your X → 9:16 Employee 5: Podcast turns long reads into audio → 11:24 Employee 6: Crumb cleans your computer → 12:07 Employee 7: Suki, your personal assistant → 13:08 Employee 8: Designer → 14:17 Employee 9: Inbox → 15:36 Employee 10: Idea Team, your advisory board → 17:21 Employee 11: Calendar → 18:06 Employee 12: Writer → 19:17 Employee 13: Marketplace Seller → 20:58 Employee 14: Job Proof → 22:27 Get every prompt free

**要点 4**
- 0:00 7 ways to make money with Grok Bot agents → 0:29 Ace: finds paid gigs and drafts proposals → 4:06 Atlas: local pages that rank on Google and ChatGPT → 7:31 Webster: $250 websites for local businesses → 11:16 Vista: Airbnb 3D walkthroughs with Higgsfield → 13:41 Rex: Google review replies for $79 a month → 16:24 Penny: flip finds from Marketplace and eBay → 19:39 Vega: stocks, forex and prediction markets → 21:31 What the bots found so far → 21:54 Part 2: how much they make

**要点 5**
- 0:00 Welcome and introductions → 1:27 From a linear relay to people and Bots working together → 4:21 A bug fixed in 90 minutes by two Bots and two people → 11:51 From automating a job to staffing a function → 13:28 The maturity curve for building trust with Bots → 18:15 Grok Bot tour: primary Bot, routines, and its own computer → 22:21 Demo: Connecting apps and routing across Grafana's repos → 53:46 Most creative use: A team of Bots running a mobile game

**要点 6**
- 0:00 Grok Bot built me an entire business → 1:10 The goal: $200 a day on autopilot → 1:51 The plan in 3 steps → 2:45 Step 1: Set up the CEO bot → 3:48 Step 2: Research ideas on X → 5:00 The 6 ideas Grok Bot found → 7:42 We pick an idea live → 7:56 Step 3: The plan and the name → 10:16 Free design trick for better websites → 12:24 Buying the domain → 13:26 What Grok Bot built in 2.5 hours → 15:00 Why we switched to a $9 one-time price → 16:47 The 3-email sequence → 17:28 Making it run on its own → 19:30 The CEO creates 5 worker bots → 22:28 What we built today and the $200 a day goal → 23:42 Get every prompt free

**要点 7**
- 0:00 How is Grok Bot useful? → 0:25 Jobs, not orders → 1:46 Grok Bot routines and skills → 10:44 Design how bots report back → 12:13 My bots have email addresses → 20:51 Platforms keep changing

**要点 8**
- 0:00 Introduction → 0:45 What is GrokBot → 3:14 Integrations and Cloud Execution → 5:15 Bot Memory System → 6:13 Drawbacks and Limitations → 11:26 Live Use Case Demos → 15:35 Best Practices for Businesses → 17:55 Conclusion and Verdict

**要点 9**
- 0:00 Intro → 0:27 Update 1 → 1:41 Update 2 → 2:57 Update 3 → 4:02 Update 4 → 5:02 Update 5 → 5:52 Bonus feature 1 → 7:01 Bonus feature 2 → 8:14 Outro

**要点 10**
- 0:00 How SpaceXAI Uses Grok Bot → 0:32 Guide 1: Any Job → 2:47 Guide 2: Sales → 4:52 Guide 3: Marketing → 6:57 The Tool I'd Add (Firecrawl) → 8:45 Guide 4: Support → 10:11 Guide 5: Recruiting → 11:52 Guide 6: Product → 12:43 The Attention List → 13:20 Outro

**要点 11**
- Go from ABSOLUTE ZERO to using AI like the people who do this for a living👇

**要点 12**
- 0:00 Intro → 0:45 My autonomous business → 2:07 Setting up an autonomous business → 9:23 Maintaining the business → 13:06 The connectors you need → 16:56 The future economy

**要点 13**
- 0:00 Grok Bot just got 11 massive updates → 0:21 Your primary bot (and how to update) → 2:48 Grok Bot now has its own email → 4:16 Your bots can read X (no X account) → 5:35 Watch it build a slide deck → 7:21 Gmail: formatting, From picker, spam search → 8:58 Link your bank accounts with Plaid → 9:57 Marketplace is now Connect Apps → 11:38 Bigger computer + custom rules → 13:05 6 small fixes you'll actually feel → 13:37 Coming: Grok Bot picks Claude Opus 5.5 for you → 14:41 Do these 5 things today

**要点 14**
- 0:00 Intro → 0:17 pstack Plugin → 1:05 Grok Bot Personal Email → 1:55 Bonus Tool! → 2:19 Built-In X Access → 3:02 The Primary Bot → 3:46 Team-In-A-Box

**要点 15**
- 0:00 Intro → 0:57 Get Grok Bot → 2:35 Create your first bot → 4:25 Connect your apps → 7:23 Put it to work → 10:25 My workflow

**要点 16**
- 0:00 Agent介绍 → 1:52 订阅与基础设置 → 3:55 连接办公应用 → 4:30 云端电脑实操 → 7:48 玩法

**要点 17**
- 0:00 上期留言：Grok Bot 放進來排在哪？ → 0:13 馬斯克提到按任務選後端模型 → 0:36 都接上強模型，四款助理還差什麼？ → 1:08 Muse：一張一張的跑腿單 → 1:26 Grok Bot：幾個固定的工位 → 1:52 dots：從頭盯到尾的一整期影片 → 2:04 Hermes：自己定規則的流水線 → 3:00 Muse 為什麼快？適合與不適合的任務 → 4:29 dots 為什麼保守？專案錯誤會往下傳 → 5:44 Grok Bot：工具共享，記憶分開 → 6:30 分開記憶的好處與邊界 → 7:17 多個 Bot 怎麼合作？群聊與幕僚長 → 7:56 回到對比表：Grok Bot 的位置 → 8:30 Hermes：關鍵在它跑在哪裡 → 9:17 本地部署，資料就一定留在本地嗎？ → 9:57 我的感受：先把手上的活拆清楚 → 10:52 下期預告：Muse 影片生成功能實測 → 11:06 留下你最想省時間的具體場景

**要点 18**
- 0:00 Grok Bot's biggest limitation → 0:57 Update 1: Automatic model routing → 1:55 Update 2: Using Claude through Grok → 2:41 Update 3: Researching X → 3:10 Connecting your AI tools with shared memory → 4:00 Controlling agents with Jarvis → 5:17 Giving Grok Bot its own email address → 6:12 Using Dots inside Agentic OS → 7:09 Agentic System Stacking: Dots + Grok → 8:36 Get my Agentic OS, course and community → 9:14 How Grok fits into my AI setup → 9:46 What to watch next

**要点 19**
- 0:00 First Impressions of Grok Bot → 0:17 Orchestrating AI Coding Agents → 0:35 Do AI Employees Actually Work? → 0:56 Persistent Cloud Environments → 1:10 My AI Agent Roster → 1:33 Browsers, Files & Terminal Access → 2:03 What Happens When a Plugin Doesn't Exist? → 2:20 Connecting Grok Bot to Cursor → 2:35 Building Software in Plain English → 2:52 Building a Real 2D Arcade Game → 3:21 The Play-Feedback-Build Loop → 4:02 Your New Role as a Builder → 4:15 Automating Agency Workflows → 5:09 Building Custom Software Integrations → 5:38 What This Means for Solo Founders → 5:55 What's Next: Full Setup Guide

**要点 20**
- 0:00 Intro: Grok vs Grok Bot → 1:30 Getting started and how usage is counted → 3:35 Tip 1: Shared workspace folder /ws → 5:29 Tip 2: imagine skill, 2K vertical images + video → 8:26 Tip 3: video-dl, download social media videos → 9:53 Tip 4: Anthropic's design skills → 10:59 Tip 5: Carousel post from a Bot group chat → 12:28 More uses: Cantonese-narrated short videos, web apps → 13:43 Wrap-up → 14:15 Easter egg

**要点 21**
- Grok Bot 官网：

**要点 22**
- 0:00 Intro → 0:34 Grok email → 2:29 Main bot → 4:56 Opus in Grok → 8:17 Cursor integration → 9:50 Tagging BOT → 10:53 Two new use cases → 12:24 Which agents you should use

**要点 23**
- 0:00 Intro → 0:18 Meet Clanker Coffee → 4:06 What each one costs → 5:49 Setup → 7:20 What only Dots can do → 10:21 What only Grok Bot can do → 13:23 What only Muse can do → 18:13 Test 1: Planning a trip to Montreal → 21:55 Test 2: A full YouTube production job → 33:49 Test 3: Running Clanker Coffee → 42:22 My verdict

**要点 24**
- 0:00 Intro → 0:37 What To Expect? → 1:29 Grok's History → 3:26 Grok Bot vs the competition → 5:15 Installation Guide → 8:49 Other Use Cases → 10:11 Pricing Breakdown & FAQ → 13:27 AI Trade Edge Community → 14:40 Outro

**要点 25**
- 0:00 The Grok Bot marketplace: bots already built for you → 0:20 1. dr eggbot builds your other bots → 2:27 2. Executive Assistant The first thing to change after you import any bot → 4:18 3. Outbound Prospecting (swap in your CRM) → 6:26 4. Signal Prospector → 6:58 5. Call Follow-Ups from every recorded call → 8:01 6. Pitch deck and partnerships coach → 8:51 7. Chief Health Officer → 9:30 8. Credit Card Max → 10:19 9. Competitor Watch → 11:19 10. last30days research bot → 12:16 My hack: make any template yours with dr eggbot

**要点 26**
- SpaceXAI just launched Grok Bot, and it changes how we use AI. Instead of asking AI questions and getting answers, you give it tasks and it does them like a job. It gets its own computer in the cloud, so it keeps working

**要点 27**
- 0:00 The rise of AI agents → 0:26 From chatbots to agents → 1:12 Grokbot and autonomous teams → 2:21 Meta's Muse assistant → 3:44 OpenAI's Dots for work → 4:42 Hermes and user control → 5:54 Market outlook and safety

**要点 28**
- This SpaceXAI Grok Bot workshop gets the most important thing about AI agents exactly right.

**要点 29**
- 500+大模型，一 key 通用， cc，cx企业开发专用： 一站式ai工作台：

**要点 30**
- 0:00 Introduction to Grokbot → 2:00 Comparison with other AI tools → 4:02 Performance in construction → 5:14 Personal assistant use cases → 8:18 Managing bot memory and tasks → 10:36 Getting started and pricing → 12:08 Workflow setup for automation → 15:05 Bot architecture and conclusion

**要点 31**
- 最近，AI Agent 又迎来一波密集发布。 从 8 月的 Grok Bot，到 9 月的 Meta Muse，再到 Manus 的 Cue 和 OpenAI 的 Dots，这些应用都在尝试让 AI 记住上下文、使用工具，并持续推进你交给它的工作。 这期视频带你认识四款近期推出的 Agent 应用，了解它们的产品定位、主要功能和典型使用场景： 🤖 Grok Bot：AI 同事与团队协作 🔵 Muse：围绕日常生活与个人目标提供帮助 💬

**要点 32**
- Go from ABSOLUTE ZERO to using AI like the people who do this for a living👇

**要点 33**
- This SpaceXAI Grok Bot workshop is wild. The real breakthrough is not that Grok 4.6 is smarter. It is that the work does not die when the chat does. Grok Bot is built as an AI teammate: it keeps the goal, uses real tools

**要点 34**
- Grok Bot Primary Bot to Cursor Cloud Agents w/ Composer 2.5

**要点 35**
- 0:00 Allow Always → 0:07 What happened → 0:21 Three agents, one question → 0:42 It is not only Muse → 1:13 Q1: Where does it live? → 1:40 Q2: What does it run on? → 2:01 Read that again → 2:07 Q3: Who can stop it? → 2:37 The Allow button, explained → 2:44 What it costs → 3:09 What people say → 3:42 Our scorecard → 4:06 Pick your fighter → 4:29 Three rules → 4:41 What to watch

**要点 36**
- 0:00 The challenges of trading → 0:55 Introduction to Grokbot → 2:12 Setting up custom bots → 3:46 Finding deals and scanning → 4:31 Risk mitigation and strategy → 8:06 Embracing AI in trading

**要点 37**
- 0:00 Introduction to Grock bots → 1:02 Automating shopping tasks → 2:35 Compound learning with bots → 3:59 Prototyping and flexibility → 5:32 Founder support and scaling → 9:04 Advanced QA and bot autonomy → 11:38 Q&A session

**要点 38**
- 0:00 開場 → 2:00 第一階段：教會 AI 穩定工作 → 5:04 第二階段：給 AI 一部電腦 → 9:13 第三階段：AI 自主分配工作 → 13:56 我想要的設計：一個入口 → 14:26 最後：AI 會變回一個人嗎？

**要点 39**
- 0:00 You are in for a ride → 1:56 Free would be pushing it → 4:07 Don't paste sensitive login details → 6:10 Do not send or publish anything → 8:11 Turn your inbox into a digest → 10:12 No need to build the recipe from scratch → 12:18 Always keep an approval step in the middle

**要点 40**
- 0:00 Dots vs Grok Bot: same agent, 7 weeks apart → 0:39 What is an always-on AI agent? → 1:01 Grok Bot: a whole team of bots → 1:22 How Grok Bot uses your apps → 1:36 Teach it by showing (my favorite feature) → 1:51 Grok Bot price and the catch → 2:29 Grok Bot scorecard → 2:41 OpenAI dots: always-on agent from DevDay → 2:56 How dots uses your apps (4,000+ plugins) → 3:10 Slack, ChatGPT Space, texting and calls → 3:38 The downside: only one dot + price → 4:02 Safety and the dots scorecard → 4:28 Dots vs Grok Bot side by side → 4:53 My take: which one should you pay for? → 5:22 Next: same task, both agents

**要点 41**
- 0:00 1 Photo + 1 Prompt: How far can 5 AI Agents do? → 0:30 Why a team of AI? How to play Grok Bot Multi-Agent → 2:08 Building AI Team: What are each of the 5 Bots responsible for? → 5:38 Officially start work: 1 Prompt to see 5 Bots collaborate on their own → 11:03 Finished product revealed: From a product photo to Live Website → 12:17 What do you think after the trial? Free Trial, Cost and Final Feelings

**要点 42**
- 0:00 GPT 給你步驟，Grok Bot 直接做完 → 0:46 Grok Bot 是什麼和 Grok 差在哪？關機也會繼續做 → 1:46 Grok Bot下載與註冊（建議先用桌面版試免費額度） → 2:22 介面導覽：把 Bot 當成 AI 員工來分工 → 3:21 交辦任務的四件事：材料、任務、約束、驗收 → 4:09 Grok Bot 教學 1｜學生：下載並分類招生文件，整理成時間軸 → 6:20 Grok Bot 教學 2｜上班族：核對 Google 試算表的收據，產出補件清單和金額加總 → 7:55 Grok Bot 教學 3｜家長：代填校外教學同意書 → 9:15 Grok Bot 教學 4｜同時派工：讓另一個 Bot 比價廚下型飲水機 → 9:50 同意書填完：看截圖核對後再送出 → 10:49 比價結果出爐 → 11:02 進階：拉群組讓多個 Bot 協作，盤點日程和產能 → 12:40 使用心得和語音輸入工具 Typeless → 13:34 不用追最強的模型，先挑一件最煩的事交出去 → 13:51 結語

**要点 43**
- 0:00 Grok Bot vs Claude: Which Should You Use? → 0:18 Part 1: AI Team Member vs AI Workbench → 2:21 Part 2: Which Tool Wins Each Use Case → 2:49 Use Case 1 (Operations): Reviewing Your Inbox and Messages → 4:19 Use Case 2: Action Items From Meeting Notes → 5:15 Granola, the AI meeting assistant → 6:57 Use Case 3: Completing Online Tasks For You → 8:42 Use Case 4: Creating Reports and Identifying Solutions → 10:00 Use Case 5 (Growth): Finding New Customers → 11:21 Use Case 6: Creating Marketing Content → 12:39 Use Case 7: Writing Sales Copy → 14:14 Use Case 8: Creating Client Proposals → 15:49 Use Case 9 (Innovation): Researching Ideas and Competitors → 16:48 Use Case 10: Building an App or Internal Tool → 18:41 Use Case 11: Automating Repetitive Work → 19:34 Use Case 12: Doing Things You've Never Thought Of → 21:03 Recap: Which Tool For Each Bucket → 21:28 Part 3: The Elephant in the Room

**要点 44**
- 0:00 Introduction: The $20 AI Agent → 0:18 Pricing & Which Plans Include Grok Bot → 1:04 Bot Scout (Research Hire & Daily Routines) → 4:37 How Grok Bot Works (Cloud VM & Browser Automation) → 6:47 Bot Clerk (Inbox Automation & Plugins) → 7:45 Custom Skills & The "Teach a Task" Feature → 11:28 Bots & Ledger, Pen, and Foreman → 13:03 Multi-Bot Group Chat & Autonomous Handoffs → 14:09 Human Controls: Approvals Queue & Takeover Mode → 15:44 3 Real-World Failures & How to Fix Them → 16:50 Who Should (and Shouldn't) Use Grok Bot → 17:28 How to Build Your First Bot This Week → 18:34 Final Verdict: What I Kept vs. What I Killed

**要点 45**
- 0:00 Free Grok Bot training that replaces $500 course → 2:15 GTM Bots: Sales pipeline automation → 6:30 Engineering Bots: Bug reproduction with screenshots → 10:45 Marketing Bots: Campaign monitoring → 14:20 Admin Bots: Calendar, travel, expenses, inbox → 18:00 How to build your own Grok Bot team from scratch

**要点 46**
- 0:00 Intro: Viral Content With Grok Bot → 1:18 Why Writing Isn't the Hard Part → 2:10 Meet the Grok Bot Agent Team → 3:38 Creating an Agent in Grok Bot → 4:12 The 5-Part Agent Instruction Framework → 5:01 Example LinkedIn Writer Agent Prompt → 7:08 Grok Bot Interface, Marketplace and Pricing → 8:24 Using AI to Write Agent Prompts → 8:59 Live Workflow: Finding Stories With Flock → 9:23 Typeless Voice Dictation (Sponsor) → 11:08 Story Shortlist and Angle Selection → 13:23 Xemingway X Post Drafts → 14:21 LinkedIn Post and Instagram Carousel → 14:58 Why I Don't Use Auto-Posting Tools → 15:33 The Full Newsroom Workflow → 16:30 Where AI Agent Teams Go Next

**要点 47**
- 本期视频，我翻遍了推特和社区里最真实的一批使用帖。一个视频带你了解Grok Bot。

**要点 48**
- 0:00 Grok Bot vs Meta Muse → 0:44 Pricing → 3:08 Grok Bot Pros → 5:12 Grok Bot Cons → 7:01 Meta Muse Pros → 10:20 Meta Muse Cons → 12:38 Meta Muse Set Custom Avatar → 14:50 Grok Bot Customization → 17:24 Meta Muse Connectors → 18:33 Grok Bot Connectors & Bot Marketplace → 19:28 Voice Dictation Meta Muse → 19:57 Voice Chat Grok Bot → 22:00 Artifacts & Dashboards Meta Muse → 23:42 Coding Challenge Meta Muse → 24:01 Grok Bot Coding Challenge → 25:49 Cursor Grok Bot → 26:30 Meta Muse Coding Challenge Results → 28:24 Grok Bot Coding Challenge Results → 31:00 Computer Use Test - Grok Bot vs Meta Muse → 37:33 Installing Apps On Meta Muse VM Computer → 38:51 Meta Muse - Feed, Ideas, Goals → 40:32 Verdict

**要点 49**
- 0:00 開場｜Grok Bot 唔止 Chatbot → 0:42 1. 幫你做完工作｜客服分流與回覆草稿 → 1:43 2. 你收工，佢繼續做｜過夜銷售研究 → 2:35 3. 示範一次，自動重複｜每週 routine → 3:35 4. 多 Bot 一齊做｜研究、創作與 CRM 交接 → 4:36 5. 唔使次次由零講起｜保留工作脈絡 → 5:35 6. 人喺邊，工作都唔會卡住｜手機交辦 → 6:27 7. X 上嘅即時聲音，變成 briefing → 7:16 由低風險工作開始｜安全上手建議與總結

**要点 50**
- 0:00 香港不用 VPN、港卡付款的 AI Agent → 0:14 像 WhatsApp 對話：一個總管分派工作 → 1:46 第一步：告訴它你的身份，自動建立機械人團隊 → 2:59 實測一：時事出題 → 5:55 實測二：活動策劃與宣傳海報 → 7:47 總結

**要点 51**
- 0:00 她睡覺時，Agent 還在修 Bug → 0:36 這不是一般的 AI 趨勢聊天 → 1:25 有環境之後，人的工作開始往後移 → 1:52 先看「管理 Agent」實際長什麼樣 → 2:48 第二個差別：任務會在背景持續跑 → 3:29 留言最有共鳴的一點：把時間留給 ideas → 4:41 真正可帶回團隊的做法：把規則寫進系統 → 6:14 那工程師還做什麼？ → 6:34 先別急著把它解讀成失業預言 → 6:49 速度越快，判斷反而越值錢 → 7:13 第三個差別：模型變便宜，平行工作會變多 → 7:47 固定 AI 預算下，單次任務越便宜就能做更多；受訪者把效率提升連到更多 Bot、更… → 8:10 現在就能試：把一個任務改成「交辦 → 驗收」

**要点 52**
- SpaceXAI’s Grok Bot has topped 400,000 weekly users roughly a month after its launch, offering an early look at demand for Elon Musk’s push into AI agents. The product, which is separate from the flagship Grok chatbot, i

**要点 53**
- 0:00 Why the Grok Bot hype is real → 1:13 Download and install → 2:00 Plans and what it costs → 3:09 Settings: usage limits and auto-review rules → 7:21 Marketplace: one-click Gmail, Calendar, ClickUp → 9:27 Bots other people built (and sharing yours) → 11:09 Your first bot → 11:52 Don't build a thousand bots → 13:23 The three memories: bot, shared, session → 14:26 Prompt A: if you already use Claude or ChatGPT → 16:25 Prompt B: if you're brand new to AI → 18:35 Chief of Staff: your first executive → 20:44 One prompt: the Chief of Staff builds the team → 23:41 Executive team group chat → 25:29 Ops team: "what do I prep for Monday?" → 27:51 Inbox manager cleans my inbox → 29:20 Import a marketplace bot into Research → 30:48 Composio: 1,000+ more apps → 32:02 Routines: the 6 AM inbox brief → 33:57 Bots can record how you work → 34:06 What you built + templates

**要点 54**
- 0:00 Introduction → 0:55 The work Grok Bot is designed to remove → 2:10 A general-purpose knowledge worker → 3:31 From chatbots to a general agent → 4:02 Grok Bot as a new agent primitive → 5:00 Live workflow: research, spreadsheet, and email → 6:13 Automation can act in your name → 9:40 Tools, context, and outcomes → 11:40 Freeing time for high-value work → 12:02 A shared computer in the cloud → 14:03 Teams of autonomous bots → 15:06 Executive and knowledge-work use cases → 16:20 Searching across company systems → 17:08 Building a software factory with Grok Bot → 19:51 When bots collaborate with bots → 23:02 A closer bot for customer follow-ups → 25:08 Teaching bots through skills and computer use → 26:14 Closing advice

**要点 55**
- 0:00 The evolving AI landscape → 1:27 Understanding Cursor projects → 3:06 Grokbot for daily operations → 4:29 Integrating tools for power → 6:45 Strategic tool adaptability

**要点 56**
- 0:00 What Grok Bot actually is (and how it compares to OpenClaw + Hermes) → 0:37 Download it + the plans → 1:05 Inside the app, usage limits, running it from your phone → 2:17 Connectors: Gmail, Calendar, Kit, and multiple accounts (finally) → 5:41 Your bot team: middle-manager bots so you only get pinged when it matters → 8:09 Routines + Grok Bot's own computer (your laptop can be off) → 10:23 Delegating: the exact prompt I gave my Chief of Staff → 14:24 Sharing a bot as a template → 14:48 The catch: memory you can't see (and how to get oversight anyway)

**要点 57**
- 0:00 Hook — Ep1 recap + what Part 2 adds → 1:00 Agenda → 2:00 Skills vs routines (definitions + order of operations) → 4:00 Teach by demonstration (step-by-step + limits) → 6:30 Save skills from chat / written instructions → 8:00 Create & test routines (schedule, events, Test run warning) → 10:30 Multiple Bots, DMs, groups, Chief of Staff → 12:30 Shared computer caveat + approval boundaries → 14:00 Three real starter workflows (safe) → 15:00 Wrap + where to read docs

**要点 58**
- 0:00 Intro → 1:00 Installation and Setup → 3:14 Computer Use and Browser Control → 5:11 Teaching New Skills → 9:10 Advanced Integrations

**要点 59**
- 0:00 Intro → 1:08 My Grok Bots → 2:51 Fixer: Home Memory → 5:30 Kin: Family Helper → 7:14 Booker: Appointments → 9:00 Coach Cal: Fitness → 11:40 Nope: Cancel Trials → 13:33 Scout: Bargain Hunter → 15:42 Weekender: Weekend Plans

**要点 60**
- ⭐️ 更多内容 👇 X: Substack: GitHub:

**要点 61**
- 马斯克推出的AI智能体 Grok Bot，到底怎么用？

**要点 62**
- 🌟 加入AI大師社群並運用AI創業賺錢： 📌 加入我的免費 Skool 社群，獲取模板：

**要点 63**
- 0:00 什麼是 Grok Bot？為什麼需要 AI 團隊？ → 5:34 Grok Bot 不同收費計劃 → 6:14 實測 Grok Bot → 22:28 擴充功能：插件及 Bot 市集 → 24:46 總結：抵用嗎？好用嗎？

**要点 64**
- 0:00 Introducing John and Peng → 2:53 The Grok Bot hype train → 4:35 Peng’s self-updating personal website built with Grok Bot → 15:12 How AI makes design more accessible → 19:35 Website update result → 20:13 John’s Figma Bro bot → 23:35 Creating marketing materials for the bot marketplace → 26:00 DevBot: from shower thoughts to working prototypes → 28:48 The trash can method of software development → 31:19 Other bots John and Peng are using → 39:05 Practical tips for when bots don’t do what you want

**要点 65**
- 0:00 grok bot explained what it actually is → 0:19 how grok bot works with your real accounts → 1:31 step 1 pick one workflow and get good at it → 2:20 step 2 sell automation bots to small businesses → 3:42 step 3 build and sell apps with grok build → 4:35 step 4 coordinate multiple bots for clients → 5:45 grok bot security risk and prompt injection → 6:53 grok bot price how much it costs to start → 7:47 how to make money with grok bot recap

**要点 66**
- 0:00 开箱 → 1:54 用grok bot管理长期项目？ → 2:54 自动检查bug → 4:06 远程调试项目 → 7:51 自动化测试报告 → 8:18 模拟真人测试？？

**要点 67**
- （无简介）

**要点 68**
- 0:00 What is Grok Bot? → 9:28 Recurring subscriptions bot → 13:29 Watcher bot → 15:07 Executive assistant bot → 20:45 Project manager bot → 23:52 Final thoughts

**要点 69**
- 0:00 Intro → 0:13 Build More Than One Grok Bot → 1:05 Grok Bots Can Collaborate → 1:56 Grok Bot Remote Computer → 3:01 Set Routines For Bots → 3:54 Grok Bots Work Remotely

**要点 70**
- ⭐️ 更多内容 👇 X: Substack: GitHub:

**要点 71**
- 0:00 The New Reality of AI-Driven Work → 0:46 Orchestrating Your AI Ecosystem → 1:59 Building Accountability with AI → 4:00 Scaling Leverage Across Your Organization → 5:50 Automating SEO and Content Strategy → 15:36 The Elon Algorithm for Process Automation

**要点 72**
- 装好一个新的 AI 工具，多数人第一反应是打开对话框聊两句，觉得也就那样，然后关掉。Grok Bot 官方文档开头就写明它不是拿来聊的，是拿来交活的：自己开浏览器、跑命令、整理文件，你合上电脑，云上的活还在跑。那拿到手第一天，先干哪一件事，才不会把它又用成一个聊天框？这集全程按 官方文档来：准入套餐与账号登录、New → Create new agent、描述那一格为什么最值钱、交活的五要素、所有 Bot 共用的那台

**要点 73**
- 0:00 The New Era of Agent-Driven Business → 1:26 Building Your First AI Chief of Staff → 2:09 Unlocking Advanced Agent Capabilities → 4:27 Pro Tips for Secure Agent Operations → 7:08 Expanding Functionality with CLI Tools → 25:41 Orchestrating a True One-Person Band

**要点 74**
- Get started:

**要点 75**
- 0:00 Introduction → 2:09 The origin story: building from scratch in one month → 8:40 Why Grok Bot was built as a separate product → 11:20 Manually onboarding a couple hundred people → 14:29 Hiding internal mechanics from users → 18:41 Timeline from beta to public launch → 19:14 Unshipping features and simplifying → 23:50 Early use cases and feedback → 26:50 Product philosophy: “Grok Bot can now” → 30:02 Cloud-first architecture → 33:12 The fresh-start advantage → 35:54 The vision: a true team of AI colleagues → 39:20 The “colleague-pilled” framework → 42:36 Work versus personal: one product or two? → 47:14 Long-lived agents, persistent memory, and the computer abstraction → 51:04 Grok Bot as an always-on infovore and chief of staff → 53:35 How fast the team moves and what preserves the startup feeling → 58:20 SpaceXAI pillars → 60:44 The first 90% vs. the last 10 → 63:30 Moving fast at scale → 66:40 How Cursor kept winning in the most competitive market in the world → 70:04 Company values: “deleting the product” and “just do the thing” → 71:45 Moats: discovered, not planned → 75:11 Tips for new users and power users → 78:00 Lightning round and final thoughts

**要点 76**
- 0:00 What the marketplace is → 0:22 Marketplace button vs plugins → 0:33 Featured creators and team bots → 1:07 Why this helps you get started

**要点 77**
- 0:00 开场：我为什么转向 Grok Bot → 0:19 订阅价格与流量怎么算 → 0:48 什么是多智能体（Agent Swarm） → 1:10 模板市场与插件 → 1:49 幕僚长架构：Anna 统筹三个 manager → 2:40 让幕僚长自己去建 bot ／ bot 间互通速度 → 3:17 谁适合用 ／ 用量、权限与云端备份 → 4:23 环境隔离：独立浏览器与登录 → 5:01 进阶：Skills 分工、教它做任务、API key → 6:23 两天下来的体会

**要点 78**
- 0:00 We've seen this before → 3:20 How Grok Bot is different from ChatGPT → 8:25 How Grok Bot is different from OpenClaw → 10:15 The Grok Bot interface → 12:35 Skills for Grok Bot → 13:17 Can Grok Bot scale a "team"? → 22:05 Questions about Grok Bot

**要点 79**
- ⭐️ 更多内容 👇 X: Substack: GitHub:

**要点 80**
- 趁頭兩個月 Grok 打折買來試用，第一天晚上就把額度燒光了！到底 $20 的 SuperGrok 能做什麼？

**要点 81**
- 0:00 Grok Bot Dropped From $200 to $20 → 1:03 What Grok Bot Actually Is → 2:14 Where It Shocked Me → 3:47 The Costs and Risks They Skip → 6:14 Grok Bot vs Hermes, OpenClaw and Claude → 8:54 Test It Free Without Wasting Usage → 9:20 Build Your AI Team With One Prompt → 10:42 Set Up a Daily Morning Brief → 11:40 Automate Inbox Triage Safely → 12:39 Build a Research Agent → 13:20 Make It Build a Live Landing Page → 15:14 My Honest Verdict

**要点 82**
- TheSignal.club is a weekly newsletter on AI x GTM (subscribe for free).

**要点 83**
- 0:00 Intro → 2:11 What Is Grok Bot? (vs ChatGPT) → 6:46 My 20 Grok Bots (Full Tour) → 13:50 Building a Grok Bot From Scratch (Live, No Code) → 21:26 Conclusion

**要点 84**
- 0:00 Intro → 0:39 What Grok Bot is → 2:48 Multi-role tester → 5:00 Support → 6:43 Churn → 8:06 Sponsor → 8:54 Competitor analytics → 9:44 AEO → 11:33 Monitoring → 12:35 Role building

**要点 85**
- 0:00 Intro: AI can now do tasks for you, not just write → 1:32 Demo: Creating a new Webmaster bot and connecting to WordPress → 6:03 Bot fully connected, live demo begins on the website → 10:09 GrokBot routines, automations, and review summarization → 13:18 Publishing books to Amazon/KDP with GrokBot → 19:30 Comparing GrokBot to Claude Code, Codex, and Cursor → 23:57 Final thoughts and how to get started with GrokBot

**要点 86**
- 0:00 Intro → 1:35 Setup & Walkthrough → 4:36 Foundation → 6:27 Use case 1 → 7:45 Use case 2 → 9:06 Use case 3 → 10:47 Use case 4 → 12:03 Build one for your work → 13:22 Honest caveats → 13:57 Outro

**要点 87**
- In this workshop, we introduce Grok Bot — a team of always-on agents with memory, tools, and their own computers — across personal and engineering use cases.

**要点 88**
- 0:00 How to approach Grok Bot → 1:17 Epic Grokbot lineup → 2:16 Suckerfish: the MVP cleanup bot → 2:57 Group chat & shopping bot → 4:06 Connect to ChatGPT / Claude → 5:24 Virtual computer & shared sessions → 6:01 IPs and danger → 7:02 Practical setup for personal ops → 7:33 Grokbot Usage → 7:41 Correction in description ⚠️ → 7:57 Pricing, Cursor vs Grok Access → 8:49 Final Take

**要点 89**
- 0:00 Why I migrated from OpenClaw to Grok Bot → 2:10 Grok Bot overview: the three core primitives → 7:41 Chief: my chief-of-staff bot → 11:51 OpenClaw vs. Grok Bot → 12:41 TradBot: my family agent → 19:51 LGTM the PR Closer → 22:03 Lockdown: SOC 2 control monitoring bot → 24:00 Holly Helpdesk: customer support → 26:58 Penny Pincher: subscription audit, insurance negotiation, Rolex shopping → 29:37 ShopZilla and Sylvie Style: personal shopping and wardrobe bots → 32:54 How to migrate your OpenClaws → 34:26 Final take

**要点 90**
- 🔗 ACCESS THE AI PROFIT BOARD ROOM

**要点 91**
- 0:00 The Paradigm Shift: From Reactive Chatbots to Autonomous Coworkers → 0:35 What Happens When an AI Gets Its Own Persistent Cloud Computer? → 1:17 Section 1: Grok Bot in Action (Outbound Sales, Invoicing & Admin Ops) → 2:28 Section 2: Industrial Compute Engine: Scaling Colossus to 200,000 H100s → 3:13 The 92-Day Hardware Sprint & Natural Gas Grid Friction (NAACP vs DOJ) → 4:15 Section 3: Architecture: Persistent VMs vs Reactive Windows → 4:55 Workflow Shadowing: Teaching the Agent via Human Observation → 5:22 Section 4: Pricing & Commercial Tiers (Super Grok Plus $100 vs Heavy $300) → 6:05 Cursor IDE Linking Protocol: Dedicated Compute Pooling for Devs → 6:31 Section 5: Multi-Agent Choreography: Autonomous Bot Group Chats → 7:33 The Economic Reality: The Evolution of Knowledge Work and Human Leverage

**要点 92**
- 0:00 Grok Bot for Business & Marketing → 0:31 Best Grok Bot Use Cases → 2:14 How We Structure Grok Bot Like an Agency → 3:29 Grok Bot Safety, Evals & Human Approval → 4:32 How to Share Grok Bot Templates → 5:14 Turn X Posts Into Grok Bot Skills → 5:39 Grok Bot for Instagram Trial Reels → 7:19 Autonomous SEO & AEO With Grok Bot → 8:34 Grok Bot for LinkedIn Outreach → 9:16 Scaling Short-Form Content With Grok Bot → 10:53 Free AI Skills on Skills Dojo → 11:34 Grok Bot for Recruiting & Hiring → 13:23 FirstMate: Grok Bot for GitHub & Development → 14:24 Revenue, Outreach & Pre-Call Intelligence → 15:12 New Grok Bot Features Coming Soon → 16:15 Grok Bot Pricing, Usage & Final Thoughts

**要点 93**
- 面对这个刚刚诞生的全新物种， 要不要换掉你手里的工具？ 这期看完你决定这波工具追不追！

**要点 94**
- 0:00 What Grok Bot Can Do → 6:00 Building Your Agent Team → 9:24 The Four Cs Framework → 12:05 Complete Grok Bot Setup → 15:09 Adding Business Context → 19:43 Building an Inbox Agent → 30:00 Feedback Loops & Skills → 38:17 Usage, Browser Control & Verification → 43:44 Multi-Agent Delegation → 50:16 Logging Work in ClickUp → 55:25 Teams, Channels & Shared Memory → 60:18 More Connections With Composio → 64:43 Building a Video Agent → 68:24 Shared Files & Context → 74:34 Automating Meeting Transcripts → 78:24 Building an AI Marketing Team → 86:41 Slack-Triggered Routines → 92:11 Sending Outputs to Other Tools → 94:28 Sharing Bot Templates → 98:57 Selling Grok Bot Services → 105:22 Final Thoughts

**要点 95**
- 0:00 What is Grok Bot / Installation → 1:58 First Bot / Chief of Staff (Bot) → 3:55 Cloud Computer / Routines → 5:00 Team Members / Specialist Bots → 6:04 Basic Settings / Functions → 7:07 Usage Limits / Final Thoughts

**要点 96**
- Grok Bot 不是又一个聊天机器人。它是 xAI 和 Cursor 一起做的「24 小时云员工」—— 有自己的 Linux 电脑、浏览器、终端，你关机它还在干活。

**要点 97**
- The AI Workflow Brief, free: Next build, make it reliable:

**要点 98**
- 0:00 Two integrations in 48 hours → 0:34 Yesterday: Link / payments → 0:55 Today: Grok Bot × X → 1:00 How to add the X connector → 1:36 Sign in, timeline access → 1:49 Draft, mentions, research, morning digest → 2:26 Why it’s a big deal → 2:31 Bundled X API credits (the old $10-per-call problem) → 3:25 Tightest AI × social integration so far → 4:07 X + a less-nerfed model → 5:34 How I’m using it (research yes, copy not yet) → 6:16 Comments / subscribe

**要点 99**
- Grok Bot has been fun and has really saved me time since I've been using it.

**要点 100**
- 0:00 Why Grok Bot routines are so powerful → 0:49 Marketing & Content → 9:42 Sales → 12:34 General Productivity → 15:53 Wrap up

**要点 101**
- 0:00 Intro → 0:33 Download Grok Bot → 1:08 Create Your First Bot → 3:39 Meet My Bot Team → 4:22 Scribe Manages Email → 7:21 Grok Bot Teamwork → 8:14 Hemingway Outlines Videos → 9:15 Seeker Monitors X → 10:54 Concierge Handles Life → 13:01 Bob Updates My Website → 14:19 More Grok Bot Ideas

**要点 102**
- SpaceXAI engineer Lauren Tan uses a team of GrokBot agents to fix bugs in GrokBot itself. Roshan Sadanani says much of GrokBot was built with GrokBot.

**要点 103**
- 0:00 <Untitled Chapter 1> → 0:44 GrokBot Basics: Agents, Skills & Plugins → 3:23 Tip 1: The MTS Bot → 6:54 Tip 2: Developer Bot (Connect to Cursor) → 10:11 Tip 3: A General GrokBot (Pinning Agents) → 12:10 Tip 4: Agents Talking to Each Other → 13:41 Group Chats → 15:29 Tip 5: Project-Specific Bots → 17:47 Command K Menu → 19:36 Tip 6: Connecting GrokBot to X/Twitter → 22:55 Tip 7: Trigger-Based Automations (Routines) → 26:15 Tip 8: Plugins & Context → 28:23 Tip 9: Multiple Email Accounts → 31:26 Tip 10: Notion Integration & Custom Skills → 32:43 Tip 11: Your Agent Has a Computer (+ Grill Me Journal) → 36:07 Recap & Resources

**要点 104**
- You don't need another chatbot. You need a teammate that finishes the job.

**要点 105**
- 0:00 I Paid $300 For Grokbot → 1:40 What Grokbot Actually Is → 3:41 The $300 Per Month Problem → 4:50 Plugins And App Connections → 5:55 Grokbot Builds 12 Sub Agents → 7:38 Porting Hermes With Claude Code → 9:30 Grokbot Vs Hermes Results → 12:02 Deploy Or Copy My Hermes → 13:22 Why I Kept Grok Anyway → 14:07 My Old Codex Review Workflow → 15:28 Grok Beats Codex Head-To-Head → 15:58 Install The Grok Build Bridge → 16:45 Final Verdict And Resources

**要点 106**
- （无简介）

**要点 107**
- 0:00 Intro → 1:08 How Grok Bot started → 6:49 Going wide with design explorations → 10:07 Early Grok Bot mascot explorations → 13:08 When John realized chat was the right UX → 16:29 Trying to push past basic chat UX for AI → 20:10 Designing onboarding concepts for Grok Bot → 22:56 John's prototyping workflow → 25:55 John's principles for designing onboarding UX → 34:51 What makes the design culture at Cursor unique → 39:09 Traits John looks for in a designer → 41:13 Interpreting feedback and data → 43:09 John's personal use cases for Grok Bot

**要点 108**
- 0:00 Setting up analytics → 1:01 Automating news updates → 2:31 Creating an executive assistant → 4:06 Monitoring competitors → 5:56 Uptime and subscriber bots → 7:07 Automating blog management → 8:51 Final reflections on AI use

**要点 109**
- 0:00 Grok Bot 真的會選股嗎 → 0:09 馬斯克收購 Cursor 後的 Grok → 0:21 Grok Bot 到底是什麼 → 0:43 誰可以使用與訂閱價格 → 0:54 Grok Build、Grok Bot 額度分開算 → 1:15 實際開啟並建立選股 Bot → 2:27 設定週一買、週五賣的規則 → 2:48 先選出台股 5 檔 → 2:57 AI 誤解了跨市場需求 → 3:28 六市場各 5 檔、共 30 檔 → 4:05 產出完整 PDF 報告 → 4:35 Grok Bot 的限制與風險 → 5:02 投資免責與下集預告

**要点 110**
- 0:00 Installation and setup → 1:05 Account login and overview → 2:02 Configuration and settings → 3:23 Creating and configuring bots → 5:53 Virtual machines and training → 6:54 Advanced automation workflow

**要点 111**
- 0:00 Introducing Grokbot → 0:40 The autonomous teammate → 1:23 Bridging the execution gap → 2:26 How the system works → 3:01 No API dependencies → 4:11 Managing bot swarms → 5:21 Learning by watching → 7:14 Real-world application

**要点 112**
- 0:00 Your Bots Don't Know What They Own → 0:18 What You'll Get From This Video → 0:37 A Computer of Their Own → 1:04 Both Bots, Same VNC Host: 0.99606 → 1:19 What This Screen Comparison Actually Proves → 1:27 What the Official Docs Say About Shared State → 1:48 What We Did NOT Test → 2:07 A Title Is Not a Boundary → 2:18 Why North Star Stays Fictional → 2:33 Building the Chief – The Contract It Accepted → 3:02 Building the Specialist → 3:11 Making Uncertainty Visible → 3:21 Role Cards Feel Slow. Here's the Payoff. → 3:48 The Work Packet: Three Public Pages → 4:10 The Delegation, Bot to Bot → 4:34 Reading the Handoff Yourself → 4:51 Why Not Just Use One Bot? → 5:10 The Team Contract Pack → 5:36 What the Specialist Returned → 6:05 Confirmed, Unclear, Next Check → 6:25 The Gap It Refused to Guess → 6:45 Verify This Against Primary Sources → 7:05 Reading the Result Against the Packet → 7:32 Stopped. Human Check Required. → 8:12 Format Is Not Verification → 8:29 Two Controls I Left Unrun → 8:48 Drawing the Line Around the Proof → 9:19 Don't Ask What You Can Schedule → 9:35 Keep Your First Team Small → 9:57 Why the Job Was Deliberately

**要点 113**
- 还在逼一个 AI 助手做所有事，结果上下文臃肿、越聊越蠢？Grok Bot 掀翻了旧习惯，但 200 美元/月的门槛让人望而却步。

**要点 114**
- 0:00 Grokbot: The AI teammate → 1:24 Cursor Origin features → 2:00 Using Cursor Origin workflows → 4:22 GLM 5.3: Affordable coding → 4:56 Zcode app capabilities → 5:38 Automating code reviews → 6:20 Meta's Musecode terminal → 7:04 Kimi K3 versatility → 7:41 Kimi's UI and research tools → 9:29 Conclusion and top tools

**要点 115**
- 0:00 Grok Bot has landed → 2:11 What Grok Bot actually is → 5:40 The $200/month price and the lock-in → 6:40 Hermes Bot Mode, the open source alternative → 12:19 Should solopreneurs bother → 20:50 ChatGPT Computer History records everything → 30:10 What's new in the AI Accelerator → 36:27 Codex limits slashed → 41:20 OxAlpha, the free mystery model → 43:50 LinkedIn's AI slop button

**要点 116**
- 0:00 Intro → 2:22 Grok Bot Overview → 3:12 What makes GrokBot special → 6:09 Pick one project → 8:09 Creating your initial agent team → 11:19 First week sprint with Grok Bot → 13:06 Automated Routines within Grok Bot → 15:51 Agent Org Structure → 17:35 Best Practices for building Agents → 19:45 Newsletter playbook → 21:12 Plugins → 22:24 Newsletter playbook pt 2 → 25:43 Sales agent → 29:06 Iterating and Improving Agents → 33:34 Grok Bot business ideas → 36:45 Site stack builder → 38:36 Directories and landing pages → 41:42 One project for a month → 42:50 Closing Thoughts

**要点 117**
- 0:00 Intro → 0:31 Email Agent → 5:44 Sponsor → 7:00 Calendar Agent → 8:13 Browser Tasks → 10:20 Coding → 12:35 DoorDash → 13:50 Meeting Summary → 15:39 Chief of Staff Agent → 16:51 Personal Agent → 17:10 School Agent → 18:21 Computer Cleanup → 19:50 Telegram

**要点 118**
- 💗如果你第一次来到我的主页，下面的文字介绍了我自己和我的频道💗

**要点 119**
- 0:00 Intro → 1:43 Grok Bot Pricing & Availability → 3:10 Getting Started With Grok Bot → 3:45 Meet Your AI Agents → 4:54 How the Virtual Computer Works → 5:57 Teach Grok Bot a Task → 6:14 Creating Your First Grok Bot → 6:59 Giving Grok Bot Access to Your Accounts → 8:16 How Grok Bots Talk to Each Other → 9:04 Connecting Grok Bot to X → 9:40 Grok Bot Is Built for Background Tasks → 10:27 My Automated Award Flight Search → 11:56 Using Grok Bot for YouTube Research → 13:19 Grok Bot Plugins & Integrations → 13:50 Using Grok Bot With Gmail → 14:31 Automating Apple Deal Posts → 15:34 How Powerful Can Grok Bot Get? → 16:16 Grok Bot Privacy & Trust Concerns → 17:24 Final Thoughts — Is Grok Bot Worth It?

**要点 120**
- 0:00 Intro → 0:34 Grok Bot made me money → 1:52 What makes Grok Bot great → 7:33 Use case 1 CEO → 10:23 Use case 2 revenue ops → 13:31 Use case 3 building → 17:17 Use case 4 repurposing → 19:35 Use case 5 management → 21:52 Use case 6 autonomous research → 23:33 Use case 7 last30days → 24:11 Use case 8 Hermes

**要点 121**
- 0:00 Welcome and project recap → 1:55 Grok 4.6 and benchmarks → 6:41 Grok bot introduction → 8:55 Agentic workflows and VM use → 23:33 Cursor Origin overview → 31:02 Demo: building a sales machine → 44:52 Conclusion

**要点 122**
- 0:00 Introduction to Grockbot → 0:39 Impact on business results → 2:56 Setup and onboarding → 6:00 Cloud capabilities → 8:17 Operational capabilities → 10:37 Comparative analysis → 13:48 Automations and workflows → 16:16 Final thoughts and feedback

**要点 123**
- 0:00 Why everyone’s quietly switching to Grok → 1:52 Grok Bot overview and setup → 3:22 My 5 Grok Bots → 4:30 The killer feature: multi-account connectors → 6:07 Grok Bot’s virtual machine and how it actually works → 6:41 Experience overview → 7:35 What I don’t love about Grok Bot → 10:08 Grok Bot use cases and my honest verdict → 12:20 Cursor Origin: the agent-native GitHub replacement → 13:47 What Origin actually looks like in practice → 14:59 Why I’m not switching from GitHub yet → 17:42 What would get me to move over → 18:52 Grok 4.6 and the How I AI Vibe bench → 20:41 Claire Index results: where Grok 4.6 ranked → 23:03 Design evals: where Grok surprised me → 25:00 My conclusion and how I’m splitting my time now

**要点 124**
- 0:00 How Grok Bot is different from ChatGPT and Hermes → 3:30 Advisor: Create and orchestrate other bots → 4:30 YouTube Researcher: Find outlier videos and content ideas → 7:00 X Scout: Surface the best posts from your feed and bookmarks → 10:30 Marie Kondo: Clean your email and save money on paid subscriptions → 15:00 Personal Concierge: Get alerts for better flight prices for your trips → 17:30 Gamer: Install and play retro games → 20:30 My honest take on privacy, pricing, and whether GrokBot replaces ChatGPT

**要点 125**
- 0:00 Intro & Course Overview → 0:30 What is Grokbot? → 2:35 The Teammate Mental Shift → 4:07 Building Your Chief of Staff → 12:06 Multi-Bot Teamwork & Chats → 17:15 Teaching Bots Custom Skills → 21:21 5 Real Automation Builds → 26:16 Limitations & AI Alternatives

**要点 126**
- 0:00 what Grokbot actually is → 0:40 the cloud computer that runs 24/7 → 1:20 the three bots I built to test it → 2:30 the shared-login trap → 3:10 teach a task (record once, reuse forever) → 3:50 where it still gets stuck → 4:30 is $200 a month worth it

**要点 127**
- 0:00 GrokBot is Here (OpenClaw & Hermes are Dead?) → 0:40 Getting Started: Cursor x Grok Setup → 0:58 The Tinder Agent Story (Why Triggers Matter) → 1:35 Hermes vs GrokBot: Why Cloud Agents Win → 2:12 Inside GrokBot: Every Agent Gets Its Own Computer → 3:00 Use Case Auto Job Search Every Hour → 3:28 Applying to Jobs with CareerOps Repo → 3:56 Applying to 100s of Jobs in Seconds (Senta) → 5:33 Use Case Video Editing Agent in the Cloud → 7:05 Use Case Daily Hacker News Image Summaries → 8:17 Teaching Skills: Price Tracking Agent (Levi's + Amazon) → 10:13 Plugins: Gmail, Vercel & Deploying Apps → 11:08 Installing Skills + Combining Agents Together → 12:43 Why This Beats Claude Code on My MacBook → 14:53 Is $200/mo Worth It? Final Thoughts

**要点 128**
- 0:00 Introduction to Grogbot → 1:47 Technical struggles → 3:24 Lessons in production → 5:49 Workflow breakdown → 7:25 Refining the pipeline → 9:43 Final assessment

**要点 129**
- 0:00 grokbot is bigger than open claw → 1:10 the agent signup pain point grok solves → 6:55 the twelve bots nate built → 9:41 what the $200 subscription actually costs → 12:25 the two bots worth starting with

**要点 130**
- How To Use Grok Bot (Quick & Easy)

**要点 131**
- 0:00 Grok Bot's own computer changes the risk → 0:55 Connected tools and app access → 1:56 Desktop and phone handoffs → 3:03 The cloud-computer demo → 3:58 Agent teams need owners → 5:02 Automations and triggers → 6:00 Why the beta label matters → 7:03 The Cursor and SpaceX claim → 8:05 A low-risk test plan → 9:01 Fair verdict

**要点 132**
- 0:00 Introduction to Grok Bot → 0:54 Setting up an agent → 4:23 Using agents for research → 7:00 Pricing and final thoughts

**要点 133**
- 0:00 What I'm covering → 0:37 Virtual computer demo → 1:22 What is Grok Bot? → 3:18 Installation & setup → 4:03 Creating your first bot → 5:25 Virtual computer walkthrough → 6:47 Shared VM across agents → 7:05 Use-Case 1 → 7:54 Teaching a task via screen recording → 9:29 Use-Case 2 → 11:05 Use-Case 3 → 11:53 Grok Bot vs Claude/ChatGPT/Codex → 14:47 Safety of sandboxed virtual machines → 15:21 Use-Case 4 → 19:52 Limitations of browser automation → 22:24 Mobile app walkthrough → 24:44 Best use cases → 25:23 Final thoughts & pricing

**要点 134**
- 0:00 概念引入 → 1:07 场景展示 → 1:21 前置条件 → 2:53 搭建步骤 → 5:30 踩坑实测 → 7:11 商业模式

**要点 135**
- 0:00 Intro → 0:33 Grokbot → 5:58 Final Thoughts

**要点 136**
- 0:00 Meet Grok Bot → 0:56 Bot Setup & Computer Use → 3:05 Connectors & Morning Routines → 7:06 Self-Improving Skills & Triggers → 11:51 Shared Skills & Agent Messaging → 13:37 Mobile Control → 14:36 Limitations & Best Use Cases → 16:01 Multi-Agent Website Build → 18:56 Avoiding Agent Hype → 20:08 Final Thoughts

**要点 137**
- 0:00 Intro → 0:54 GrokBot Positioning → 2:42 Connecting to Plugins → 3:07 Use Case → 4:00 Collaborate with other AI Agents → 6:12 iOS App → 7:08 Cloud Computer → 9:52 Pricing and Free Trial

**要点 138**
- 0:00 Introduction to Grok Bot → 1:38 Use Case: Building a support agent → 3:48 Creating a new custom support bot in Grok Bot → 5:01 Instructing the agent to connect to a specific support email → 6:41 Connecting and authorizing the Gmail connector → 7:26 Reviewing the automated 'Support Inbox Watch' routine → 9:48 Using Claude Code to generate a knowledge base for the agent → 12:56 Uploading the knowledge base file to the Grok Bot agent → 14:16 Testing the agent with a live email and reviewing the drafted reply → 15:49 Approving the draft and sending the automated reply → 16:36 Final thoughts on Grok Bot's simplicity and effectiveness → 18:08 Promoting the AI apps community and conclusion

**要点 139**
- 0:00 Grok Bot 发布 → 4:00 功能、配置、工具、记忆与 Skills → 6:49 实测

**要点 140**
- SpaceXAI just dropped a bombshell with the Grok Bot update, and it changes everything we thought we knew about AI assistants. We are moving past simple chat windows into an era of persistent digital coworkers that can lo

**要点 141**
- 0:00 Introduction to Grok Bot → 0:46 Grok Bot plugins for business automation → 2:22 Luma event coordinator demo → 3:43 Connecting Vercel for automated dashboards → 5:41 Building a presentation filtering app → 6:39 Multi-agent collaboration in a single chat → 7:36 Brainstorming an iOS food logging widget → 10:12 Setting up automated routines → 11:49 Agents collaborating on the iOS widget → 13:26 Grok Bot mobile app interface → 15:45 Grok Bot pricing and Cursor plans → 17:09 The Teach Task feature → 18:44 Grok Bot vs Buzz → 20:02 Grok Bot vs ChatGPT and OpenClaw → 21:46 The Potato P-stack plugin explained → 24:15 Redesigned app built by Potato mode → 28:01 Grok Bot vs Hermes Agent → 32:03 Matt Schumer's Grok Bot review → 34:40 Macro brief generation use case → 37:01 Why Exa for search over Firecrawl → 44:04 How to create a new Grok Bot → 49:24 Personal Cursor usage statistics → 53:30 Luma event workflow recap → 58:32 Justifying the Grok Bot cost → 63:16 Teaching an agent a task live → 67:06 Routines for personal health tracking → 70:02 The week's big announcement reveal → 73:42 Tips for App Store submission

**要点 142**
- 0:00 Intro → 1:42 Creating new Grokbot with Connections → 5:25 The Plugins Tab → 6:40 How Skills, Plugins, and Agent Descriptions Work → 8:08 Grok Bot Cloud Computer → 10:24 Agents Talking to Each Other → 13:38 Triggers & Automations → 17:06 What's Missing & Coming Soon (Probably)

**要点 143**
- Today we’re launching Grok Bot, AI teammates you can give real work to.

**要点 144**
- 0:00 Intro → 0:16 Meet the Bots → 1:23 First Bot → 4:26 Always-On Computers → 5:39 Plugins → 6:35 The Bot's Computer → 7:11 Teach Tasks → 7:46 Routines → 8:31 Mobile App → 9:32 What I Like → 11:08 What I Dislike

### 11C. B 站视频索引

本版（无来源版）省略 B 站视频标题与链接索引；有逐步内容的演示已收录在 11A。

## 12. 常见问题与坑（FAQ & Pitfalls）

> 以官方帮助中心和 Cursor 论坛员工的回复为主。论坛 8–9 月的回答可能早于 2026-10-12 的套餐调整，括号里都注明了日期。

### 12.1 账户、套餐、计费
| 问题 | 答案 / 处理 |
| --- | --- |
| 官方公布每档的具体额度吗？ | 没有，只给相对关系（Pro < Pro+ < Ultra） |
| SuperGrok 和 Heavy 除了额度还有别的区别吗？ | 没有，只是额度大小不同，没有延迟或优先级差异；Ultra 和 Heavy 同档、不叠加 |
| 建例程时提示 "Automations are not available with your plan" | 例程会检查你的 Cursor 套餐；Cursor Start 套餐即使链接了 SuperGrok 也没有 Automations（10-13） |
| 用量页的 "Free" 和 "Included" 行是什么 | Free = Bot 自己的后台检查（主动性功能），不收费；Included = 例程运行，计入每周额度 |
| Grok Bot 启动的 Cloud Agent 扣哪里的钱？ | （09-30 员工答复）计入 Cursor 用量，不是 Grok Bot 池子；10-12 之后 X Subscription 合并成一个池子 |
| 按需付费是怎么计算的 | 按 token 计费，没有固定的"美元→请求数"换算；按需上限是整个账户统一的 |
| 合并账户后旧 Bot 不响应 | 10-14 已修复，发一条新消息就行；还不行就联系 support@x.ai |
| 想新开一个团队 | cursor.com/unify/team 只能转换**已有的** Cursor 团队；没有团队要用 "Start a team" |
| 试用到期后显示 "Can't reach your computer" | 试用结束导致的（1 周试用） |
| 怎么联系客服、看版本号 | **Support → About** 查看版本；帮助页 ；删除账户 |

### 12.2 电脑、文件、网络
| 问题 | 答案 / 处理 |
| --- | --- |
| 重建后装的软件包没了 | 文件（workspace 和 home）和登录状态会保留；系统层（apt 包、系统目录、应用、守护进程）**不保留**，不要依赖它们 |
| 文件莫名回到几天前 | 出现过快照悄悄还原（10-02），磁盘满时也会悄悄回滚（10-15）→ **重要文件在云电脑之外另做备份** |
| 磁盘突然只剩 16 GB | 分配回退的 bug，10-06 已修复 |
| 内存不够、Chrome 崩溃（Error code 9） | 所有 Bot 共用一台电脑，内存是固定的；同时跑多个浏览器任务会被杀进程；闲置 Bot 的桌面会一直开着（关闭闲置桌面的修复已经做好但还没上线，10-15）→ 例程错开时间，让 Bot 关掉浏览器；Reset 可能要 1 小时左右，**进行中不要再点** |
| MySQL/SSH/IMAP/SMTP/FTP 连接超时 | 10-01 起部分电脑只放行 443 端口 → 改用 HTTPS 端点、git 走 HTTPS；"经此电脑转发流量"只覆盖浏览器 |
| 美国 API 返回 HTTP 451 | 出口 IP 可能被定位到中国，目前没有选择地区的设置 |
| 能连公司 VPN 吗？ | Bot 电脑不能加入公司 VPN，**不要在 Bot 电脑上装 VPN 客户端**（会把它搞掉线）；企业私网请看官方文档 |
| 打开 App 一片白屏 | Cloudflare WARP 拦截了流量 |
| 本地执行连不上或超时（Windows） | 杀毒软件/VPN 可能拦截：Kaspersky；F-Secure（只能排除整个 Temp 目录）；Surfshark 实时防护会多出约 11 秒，超过 10 秒的超时；Zscaler 需要放行 *.cursorvm.com |
| 改了密码后报 "Authentication error" | 改密码会让 Bot 电脑的后台会话退出，大约 30 分钟后自己恢复 |
| Passkey/安全密钥登录失败 | 选 "Try another way"，改用密码或邮箱验证码；可以说"hand me your computer so I can log in to X" |
| Linux RPM 下载链接失效 | 员工给过直链；**不要用非官方的下载链接** |

### 12.3 例程、webhook、Bot 协作
| 问题 | 答案 / 处理 |
| --- | --- |
| 例程没在预定时间跑 | 美国早高峰时段曾被跳过，09-15 起每个例程有预留时段；10-08 高负载下很多例程被延后一整天，**错过的不会补跑**，需要手动运行；报 "Activity task failed" 是模型容量问题 |
| 找不到 webhook URL | 点聊天里 "Created routine" 那一行的例程标签 → 例程页显示 POST URL、key、header（点击复制）；或者直接问 Bot |
| webhook key 泄露了 | 只有 URL 触发不了，必须带 key；轮换方法是删掉例程重建（旧 key 失效）；暂停可以停用；只在 Cursor dashboard 的 API keys 页删 key 是不够的 |
| Bot 说"稍后做"，结果没做 | 它没有给自己排唤醒；不紧急的 Bot 间消息不会唤醒对方 → 让它建例程/提醒 |
| Bot 之间互相唤醒，额度用光 | "stay quiet"只是提示；用一个总指挥 + 子代理的结构，删掉不用的专员 |
| 3 天就用了 99% 的 Ultra 额度 | 计费的是干的活（工具、浏览器、子代理、每轮重读对话）；一个任务开一个新对话；拉开例程间隔（几分钟一次 ≈ 每天 100 次） |
| 怎么让 Bot 真正遵守规则 | 用 Auto-review 的 Ask first；聊天里定的规则存在记忆里，新的优先、旧的会被挤掉 → 定期让 Bot 把规则合并成一份短清单 |
| 模板导出后技能是空的 | 已知 bug（10-12 时仍在跟踪） |
| 发布公开模板报 "A team must be selected" | 账户里残留了 0 席位的团队成员身份 → 发邮件给 hi.com |
| 技能同步卡住 | 技能文件夹里有 node_modules（约 9000 个文件） |
| Grok 启动的 cloud agent 在哪里看 | cursor.com/agents → 筛选 Source > Grok Bot；/review-bugbot 对 Grok 启动的代理不可用 |

### 12.4 连接器、邮件、X
| 问题 | 答案 / 处理 |
| --- | --- |
| X 账号被冻结 | 有人让 Bot 用浏览器自动化发帖和回复，结果账号被冻结 → 遵守 X 的自动化规则，用草稿 + 人工发布 |
| 1Password 连接断了，提示 "delivery outcome is unknown" | 条目 URL 超过 2000 字符导致的，10-20 已修复 |
| 在 grok.com/Grok 手机 App 上点邮件草稿的 Send，结果发成了聊天消息 | 已知问题；另外，只读的 Gmail 连接发不了信（10-18） |
| Bot 邮箱能改名吗？ | 不能（功能请求已合并） |
| Gmail 能列出附件但下载不了 | 当时（08-24）的限制，改用浏览器下载 |
| GitHub 插件一直显示 "Needs auth" | 非 OAuth 连接器的这个标记只是显示问题；GitHub 插件用的是 Setup Values 里的 token |
| Zoom 报 4700，Notion/Canva 报 invalid redirect | Zoom：官方已知问题，没有绕过办法；Notion、Canva 是特定版本的问题 |
| 存在电脑上的密钥过夜被清掉 | 09-08 的 bug（重连时被覆盖）→ 用 Secure secret 存 |
| 不同 Bot 能用不同的 Google 登录吗？ | 目前浏览器会话是共享的，一个 Bot 登录/退出会影响所有 Bot |

### 12.5 新手最常踩的 10 个坑（综合）
1. 以为每个 Bot 有自己的电脑 → 其实是**一台共享电脑**（6.1）。
2. 用 8 月的教程查价格 → **10-12 起是 X Subscription**（2.1）。
3. 例程设得太频繁 → 一天用掉一周额度（4.6）。
4. 以为点了 Added 就连上了 → 还要 **Authenticate**（3.1）。
5. 把密码、API key 贴进聊天 → 用 Secure secret，贴了就轮换（3.7）。
6. Bot 说"稍后做" → 不会自动做，要建例程（12.3）。
7. 只用口头规则约束危险操作 → 用 **Ask first**（10.1）。
8. 重要文件只放在云电脑上 → 要异地备份（12.2）。
9. 用浏览器在 X 等平台自动发帖 → 有封号风险（12.4）。
10. 照搬 8–9 月视频里的菜单 → 界面已经改版（2.4、4.3）。

---

## 13. 社区资源索引

本版（无来源版）省略社区资源链接列表；操作说明与步骤请见第 1–12 节及附录。

## 附录 A：版本与发布时间线

> 产品功能发布时间线（事实日期），已去掉发布链接。


> 官方 帖子的日期按 UTC 记录（北京时间可能是第二天）；changelog 的日期以 x.ai 页面为准。

| 日期 | 事件 |
| --- | --- |
| 2026-08-11 | Grok Bot early beta 发布 |
| 08-12 | v0.17.0：新增 Teams/Linear/Sentry/PagerDuty 例程触发；v0.18.0：Cancel Trial |
| 08-18 | 手机通知按 Bot 分组 |
| 08-19 | v0.21.0：webhook 例程 |
| 08-21 | 开放给 SuperGrok Plus / Cursor Pro+ / Teams，并提供免费试用 |
| 08-26 | 开放给所有 SuperGrok 和 Cursor Pro 用户；重置额度 |
| 08-28 | 分享模板；通过 Link 购物 |
| 08-29 | 连接 X 时自动创建开发者账户 |
| 08-31 | Outlook / Calendar / OneDrive |
| 09-02 | Android 版 |
| 09-03 | Enterprise 版 |
| 09-04 | 市场模板（Haggle Bot） |
| 09-05 | 重置额度 |
| 09-08 | 聊天内表单/登录，支持任意密码管理器 |
| 09-09 | 内联草稿；v0.48.0：多账户、新市场 |
| 09-10 | GTM 连接器 |
| 09-11 | Microsoft Teams；v0.50.0：经你的电脑转发流量 |
| 09-15~17 | "3 天建一家公司"直播 |
| 09-16 | 1Password |
| 09-17 | 语音对话 |
| 09-18 | 语音便条；v0.57.0：Projects |
| 09-22 | 客服用 Grok Bot 重建；界面优化 |
| 09-26 | Finance 集成；v0.61.0：Team Bots |
| 09-30 | 交接编码给 Cursor、GitHub/Origin 插件 |
| 10-01 | 主动建议 |
| 10-02 | 重置额度；v0.66.0：主 Bot；v0.67.0：Connect Apps 改版 |
| 10-05 | v0.68.0：新语言、例程右键菜单 |
| 10-07 | 内置 X 搜索/阅读/监控；v0.68.1：PPT、Bot 颜色、1920×1200 |
| 10-08 | Shopify |
| 10-09 | Bot 邮箱 |
| 10-11 | v0.70.2：邮箱、X 搜索、应用内卡片、X Money / Stripe Link、Glean、QuickBooks |
| 10-12 | The X Subscription |
| 10-14 | v0.71.2：plus 地址、附件上限提高、200 个插件技能、管理员可关闭 Bot 间消息 |
| 10-16 | 聊天卡片重新设计 |
| 10-18 | X 私信、Robinhood/Coinbase/Trello 等（视频口述，changelog 里还没有） |
| 10-19 | iPhone 操作按钮 |

## 附录 B：需注意的说法差异（事实提醒）

> 不同渠道对同一功能的说法有时不一致。下面只保留实用提醒，不罗列出处。

| 主题 | 实用提醒 |
|---|---|
| 每个 Bot 有自己的电脑吗 | 以产品文档为准（6.1） |
| 描述字段 | 以帮助中心/应用内说明为准（2.4） |
| 附件上限 | 以较新的更新日志为准（6.2） |
| Auto-review 设置位置 | 两处都找一下（10.1） |
| `/` 引用技能 | 用自然语言或 `@`（5.2） |
| Bot 邮箱发信 | 默认按需要批准对待（3.6） |
| 邮箱能否改名 | 以帮助中心/应用内说明为准 |
| Gmail 多账户 | 以帮助中心/应用内说明为准（3.3） |
| 套餐与价格 | 看 2.1 |
| 默认模型 | 以产品官方说明为准（1.4） |
| X 免费读取额度 | 未证实，仅供参考（3.5） |
| Teach a task 的产出 | 技能 → 再决定要不要做成例程（5.3） |
| X 私信 | 以应用内最新行为为准 |
| 香港能不能用 | 以 App 内实际情况为准（2.1） |

## 附录 C：资料缺口说明

本版已去掉外链与出处标注。下列缺口仍会影响细节完整度：

1. 部分公开视频没有可用字幕，只能按章节标题整理，无法逐步还原。
2. B 站等内容因接口限制，本次没有逐步操作笔记（本版已省略标题索引）。
3. 小红书、知乎、Reddit、V2EX 等需要登录或有反爬限制的站点，基本未覆盖。
4. 计费与套餐以应用内和当前订阅说明为准；早期教程里的价格档位可能已过时。
5. 较新功能若更新日志尚未列出，请以应用内实际菜单为准；未证实的说法已在正文标出。
6. 各订阅档的具体额度、以及部分免费读取限额，官方未公布具体数字。

