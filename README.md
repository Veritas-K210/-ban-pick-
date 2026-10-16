<div align="center">
  <img src="assets/logo.svg" width="104" alt="DBD Killer Ban/Pick Tool">
  <h1>黎明杀机 · 屠夫 Ban/Pick 工具</h1>
  <p><b>BO1 / BO3 / BO5 / BO7 / BO9</b> 赛制 · 随机角色池 · <b>Ban 与 Pick 穿插流程</b> · 赛事与自定义预设</p>
  <p>
    <a href="../../actions/workflows/ci.yml"><img src="../../actions/workflows/ci.yml/badge.svg" alt="CI"></a>
    <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT"></a>
    <img src="https://img.shields.io/badge/dependencies-0-brightgreen.svg" alt="零依赖">
    <img src="https://img.shields.io/badge/build-single--file-orange.svg" alt="单文件产物">
  </p>
</div>

一个给《黎明杀机》（Dead by Daylight）赛事、直播与开黑用的**屠夫 Ban/Pick 工具**：
只做杀手的 Ban/Pick，从所有屠夫中随机抽取本场角色池，双方轮流 Ban 与 Pick，
**Ban 之间穿插 Pick**，最后固定剩下 1 位屠夫作为「剩余杀手」，并自动排出带序号、带归属队伍的选择顺序表。

零依赖、零构建环境要求，产物是**一个可离线双击打开的 HTML 文件**，也可以直接托管到 GitHub Pages。

<!-- 界面示意图（不是截图，按真实布局绘制） -->
![界面示意](assets/ui-preview.svg)

---

## 快速开始

### 方式一：直接下载单文件（推荐给使用者）

下载 [`dist/index.html`](dist/index.html)，双击用浏览器打开即可，**完全离线**，不需要联网、不需要安装任何东西。

### 方式二：GitHub Pages（推荐给赛事/社群）

Fork 本仓库 → 打开 `Settings → Pages` → Source 选择 **GitHub Actions**，
之后每次推送 `main` 分支会自动构建并发布，访问 `https://<你的用户名>.github.io/<仓库名>/` 即可。

### 方式三：本地开发

```bash
git clone https://github.com/<你的用户名>/dbd-killer-banpick.git
cd dbd-killer-banpick

npm run dev        # 起一个零依赖静态服务器 → http://localhost:4173 （直接调试 src/）
npm run build      # 生成单文件 dist/index.html
npm test           # 109 项逻辑回归 + 构建产物自检
npm run release    # 生成文档 + 构建 + 测试
```

> 也可以**直接双击 `src/index.html`**：源码刻意使用「经典脚本 + 共享作用域」而不是 ES Module，
> 因此不需要构建、不需要 http 服务器就能运行，同时 `scripts/build.mjs` 又能把它们内联成单文件。

---

## 核心特性

- **只做屠夫**：Ban/Pick 面板里只有杀手，不含逃生者、地图与技能。
- **穿插流程**：Ban 与 Pick 交替进行，节奏由工具协调（见下表），不再是「先 Ban 完再 Pick」。
- **严格轮流**：双方 A→B→A→B 交替出手，不会出现连续两手属于同一队。
- **固定剩 1 位**：总步数 = 角色池 − 1，流程走完必定只剩 1 位既没被 Ban 也没被 Pick 的屠夫，
  点「🏁 完成选择」即把它作为「剩余杀手」加入结果池，并永远排在顺序表最后一位。
- **两种流程模式**：
  - 穿插流程（默认）：顺序锁定，点卡牌即执行当前那一步，适合正式比赛；
  - 自由模式：Ban / Pick 两套计数独立、可随时切换，另可选「严格模式」要求先 Ban 完才能 Pick。
- **随机角色池**：按赛制自动抽取（BO1→5、BO3→11、BO5→17、BO7→23、BO9→17 位），支持重新抽取。
- **自定义角色池与预设**：44 位屠夫可任意勾选，内置 6 组赛事 / 社区预设，
  支持「保存为我的预设 / 覆盖 / 删除 / 导出 JSON / 导入 JSON」，赛事预设与个人预设分开保存、互不影响。
- **完善的结果输出**：带序号的「选择顺序表」（标注是哪个队选的、第几局）、分队列出的 Ban 名单、
  完整「行动日志」（穿插流程每一步）、一键复制文本结果。
- **比赛友好**：撤销（Ctrl+Z）、重置流程、先手方切换、刷新页面 12 小时内自动恢复未完成的对局。
- **零依赖 / 离线**：无框架、无 CDN、无网络请求，单文件即可分发；屠夫名单可用界面直接补充新屠夫。

---

## 赛制规则

| 赛制 | 随机角色池 | 全局 Ban | 每队 Ban | 每队 Pick | 剩余杀手 | 结果总数 |
| BO1 |     5      |    4    |     2     |     0    |    1    |     1   |
| BO3 |     11     |    8    |     4     |    1    |    1     |    3    |
| BO5 |     17     |    12   |     6     |    2     |    1    |    5    |
| BO7 |     23     |    16   |     8     |    3     |    1    |     7   |
| BO9 |     17     |    8    |     4     |    4     |    1    |    9    |

规则推导：

```
结果总数 = BO 数
每队 Pick = (BO 数 − 1) ÷ 2          → BO3:1  BO5:2  BO7:3  BO9:4  BO1:0
全局 Ban = 角色池 − BO 数（必为偶数，两队均分）
剩余杀手 = 角色池 − Ban − Pick = 1 位（永远排在选择顺序表最后）
```

### 穿插流程节奏（双方轮流 A→B→A→B…）

| 赛制 |                                 流程                                     | 步数 |
| BO1 | 各禁 2                                                                    | 4 |
| BO3 | 各禁 2 → **各选 1** → 各禁 2                                               | 10 |
| BO5 | 各禁 2 → **各选 1** → 各禁 2 → **各选 1** → 各禁 2                         | 16 |
| BO7 | 各禁 2 → **各选 1** → 各禁 2 → **各选 1** → 各禁 2 → **各选 1** → 各禁 2    | 22 |
| BO9 | （各禁 1 → **各选 1**）× 4                                                 | 16 |

实现方式（见 [`src/js/core/rules.js`](src/js/core/rules.js)）：
每队各有 `n = 每队Ban + 每队Pick` 个回合，工具把该队的 Pick 回合**均匀铺开**（两侧留 Ban），
双方再严格交替，于是整体自然形成「各禁 N → 各选 1 → …」的节奏，且总步数恒等于角色池 − 1。

---

## 界面与操作

1. **选赛制**（BO1/3/5/7/9）、填两队名称、选择先手方；
2. **选角色池来源**（全部屠夫 / 赛事预设 / 我的预设 / 手动勾选），点「🎲 抽取角色池」随机抽签；
3. 按顶部**时间轴**提示依次点击屠夫卡牌（工具会显示「第 N 步 · 哪队 · Ban 还是 Pick」），
   全部走完后点「🏁 完成选择」把最后剩下的屠夫加入结果池，点「📋 复制结果」导出文本。

快捷键：`1` Ban 模式、`2` Pick 模式、`Ctrl/⌘ + Z` 撤销。

---

## 角色池预设

内置预设（**每个预设单独保存**，都可在界面上自由增删后另存为自己的版本）：

|           预设          | 可抽取屠夫 |                        说明                            |
|         全部屠夫        |    44      | 默认，不做限制                                          |
| DBDL Season X（参考池） |     42     | 依据 Liquipedia 逐局记录归纳（护士 / 枯萎者 全赛季未出场） |
| DBD World Cup（参考池） |     43     | 依据逐局记录（护士仅出场 1 次）                           |
| 虎牙万圣赛（明文禁用）   |     41      | 规则原文：禁用 护士、枯萎者、怨灵                          |
| PHG 1v1 固定 16 屠夫池  |     16     | 公开规则中的固定白名单                                    |
| OSL（参考池）           |     42     | 官方名单需人机验证无法公开获取，属占位参考池                |
| 竞技通用池 · 无 S 级     |     40     | 社区通用约定：排除护士、枯萎者、怨灵、艺术家                |

> ⚠️ DBDL / OSL / 世界赛的官方规则书（Google Docs、赛事官网）目前无法公开抓取，
> 因此内置预设均为**参考池并标注来源**，不是官方名单。详见 [`docs/presets.md`](docs/presets.md)。

---

## 屠夫名单

收录 **44** 位屠夫（截至 Chorus of Sin 章节），中文名采用官方/赛事名单译名
（如 迈克尔·迈尔斯 / 妖巫 / 食人魔 / 门徒 / 鬼面 / 鬼武士 / 死亡枪手 / 处刑者 / 连体婴 /
骗术师 / 追踪者 / 影魔 / 操纵者 / 白骨商人 / 好孩子 / 未知恶物 / 巫妖 / 训犬师 / 食尸鬼 / 一号 / 审判者），
旧社区叫法（皮脸、猪女、威斯克、金木研、弹簧陷阱……）保留为搜索别名。

完整名单与新增屠夫的方式见 [`docs/killers.md`](docs/killers.md)。

---

## 项目结构

```
dbd-killer-banpick/
├─ src/                        # 模块化源码（经典脚本，共享作用域，可直接双击运行）
│  ├─ index.html               # 页面骨架 + 构建占位标记
│  ├─ styles/main.css          # 全部样式（主题变量 / 布局 / 组件）
│  └─ js/
│     ├─ data/killers.js       # 屠夫总表数据
│     ├─ data/presets.js       # 赛事与社区预设、赛制常量
│     ├─ core/store.js         # 本地存储（预设、自定义屠夫、断线恢复）
│     ├─ core/state.js         # 对局状态
│     ├─ core/rules.js         # 规则计算 + 穿插流程序列生成
│     ├─ core/actions.js       # 抽池 / Ban / Pick / 结算 / 撤销
│     ├─ ui/render-*.js        # 各区域渲染
│     ├─ ui/export.js          # 结果文本导出
│     ├─ ui/preset-io.js       # 预设应用与 JSON 导入导出
│     └─ app/{events,main}.js  # 事件绑定与启动
├─ tests/suite.fragment.js     # 逻辑回归断言（拼接到应用源码后、同一作用域内执行）
├─ scripts/
│  ├─ build.mjs                # 内联打包 → dist/index.html
│  ├─ test.mjs                 # 测试入口（构建自检 + 逻辑回归）
│  ├─ gen-docs.mjs             # 由数据生成 docs/killers.md、docs/presets.md
│  ├─ serve.mjs                # 零依赖静态服务器
│  └─ dom-stub.mjs             # Node 下的极简 DOM 桩
├─ docs/{rules,presets,killers}.md
├─ assets/{logo,ui-preview}.svg
├─ dist/index.html             # 构建产物：单文件、离线可用（GitHub Pages 直接发布这个目录）
└─ .github/workflows/{ci,pages}.yml
```

---

## 开发

### 常用命令

|        命令         |                      作用                         |
| `npm run dev`       | 启动静态服务器调试 `src/`（http://localhost:4173） |
| `npm run dev:dist`  | 调试构建产物 `dist/`                               |
| `npm run build`     | 生成单文件 `dist/index.html`                      |
| `npm test`          | 构建产物自检 + 109 项逻辑回归                      |
| `npm run docs`      | 由代码数据重新生成文档                             |
| `npm run release`    | docs + build + test 一条龙                        |

### 新增屠夫

在 [`src/js/data/killers.js`](src/js/data/killers.js) 末尾追加一行后运行 `npm run release`：

```js
{id:'newkiller', cn:'中文名', en:'The New Killer', alias:'旧叫法 别名 newkiller', year:2026, ch:'章节名'}
```

不想改代码时，界面里「② 角色池来源 → 手动编辑 → ＋ 添加到屠夫总表」也能临时补充（保存在本机浏览器）。

### 新增赛事预设

在 [`src/js/data/presets.js`](src/js/data/presets.js) 的 `BUILTIN_PRESETS` 里加一项即可
（`ids` 写固定白名单，或用 `exclude` 从总表里排除）：

```js
{
  id:'my-event', name:'某某杯（参考池）', cat:'赛事预设',
  source:'https://example.com/rules',
  note:'说明这个池子怎么来的、排除了谁。',
  exclude:['nurse','blight']
}
```

### 设计约定

- **零运行时依赖**：不引入框架 / 打包器，`scripts/build.mjs` 只做内联拼接。
- 源码为经典脚本并共享作用域，因此新增文件必须同时登记到 `scripts/build.mjs` 的 `JS_FILES`
  （测试会校验 `src/index.html` 的引用与 `JS_FILES` 完全一致）。
- 所有面向用户的文案使用中文；代码注释保持中文。

---

## 常见问题

**Q：刷新页面后对局没了？**
12 小时内未结束的对局会自动恢复；已结束的对局不会恢复。点「重置流程 / 重新抽取」可清空。

**Q：某个赛事的池子和内置预设不一样？**
预设有的是参考池。直接在「手动编辑」里勾选成实际名单，再「保存为我的预设」，之后下拉框里就能复用；
也可以用「导出 JSON」把池子发给队友导入。

**Q：BO9 的角色池为什么是 17 位？**
按需求设定（BO9 → 17）。若你们赛事用别的人数，在「高级：手动覆盖角色池 / Ban / Pick 次数」里改即可，规则会自动重算。

**Q：想固定抽 9 人池？**
把「角色池抽取数」填 9 就行（此时 BO1 会变成每队 4 Ban、剩 1 位）。

**Q：能同时管逃生者 / 地图 / 技能吗？**
本工具只做屠夫 Ban/Pick。逃生者与地图可另开一份页面互不干扰。

---

## 免责声明与合规说明

**本项目是非官方的社区工具**，与 Behaviour Interactive 及《黎明杀机》（Dead by Daylight）官方
**无任何关联，未被授权、认可或赞助**。游戏名称、角色名称、赛事名称及相关商标归各自权利人所有。
**本项目的想法来自作者原创，由AI进行代码编写**

为了把风险降到最低，本项目刻意遵守以下约定（也请贡献者一并遵守，见 [CONTRIBUTING.md](CONTRIBUTING.md)）：

| 约定                        | 说明                                                                   |
| **不使用任何游戏素材**     | 不含游戏立绘、图标、UI 截图、音效、字体；界面与 README 中的图都是自绘 SVG    |
| **只做指代性使用**        | 屠夫 / 游戏名称仅用于指代对应角色与作品，不作为本项目的品牌或 Logo            |
| **不暗示官方关联**        | 项目名、页面标题与页脚均标注「非官方」；不注册含商标的域名                     |
| **赛事名仅作参照**        | DBDL / OSL / 世界杯等名称只用于说明「参照哪套规则」，与主办方无隶属或合作关系  |
| **预设标注为参考池**      | 每个预设都显示来源与局限，正式比赛以主办方公布规则为准                         |
| **非商业**               | 项目不含广告、付费内容与周边；如接受赞助，请注意这仍可能被视为商业使用          |
| **不整段复制规则文本**    | 只整理「哪些屠夫被禁 / 出场」这类事实性信息，并注明来源链接                    |

### 数据来源与署名

| 内容               | 来源                                                                                                                | 说明                        |
| 屠夫名单与实装章节  | [deadbydaylight.wiki.gg](https://deadbydaylight.wiki.gg/wiki/Killers) 等公开 wiki                                    | 名单属事实性短名称集合 |
| 中文译名           | 官方 / 赛事名单译名                                                                                                   | 界面与文档统一采用，旧社区叫法作为搜索别名 |
| DBDL Season X 预设 | [Liquipedia](https://liquipedia.net/lab/api.php?action=parse&page=Dbd/Dbdl%20season%20x&format=json&prop=wikitext) | Liquipedia 内容采用 **CC-BY-SA 3.0**，此处按其要求署名并给出链接 |
| DBD World Cup 预设 | [Liquipedia](https://liquipedia.net/lab/api.php?action=parse&page=Dbd/Dbd%20world%20cup&format=json&prop=wikitext) | 同上 |
| 虎牙万圣赛禁用名单   | [虎牙赛事公告](https://blog.huya.com/mblog/index?detail=true&id=49125&tab=news)                                     | 规则明文禁用护士 / 枯萎者 / 怨灵 |
| PHG 1v1 固定 16 池 | [PHG Balanced DBD 规则](https://mcpleh.github.io/PHGBALANCEDBD/rules1x1.html)                                        | 公开规则页 |
| OSL                 | —                                                                                                                   | 官方名单无法公开获取，内置为占位参考池 |

> 以上为工程实践层面的整理，**不构成法律意见**。若要用于商业用途（广告、付费、周边等），
> 建议先咨询专业律师，并查阅 Behaviour Interactive 的粉丝内容 / 品牌使用政策。

## 许可

[MIT](LICENSE)

---

<details>
<summary><b>English summary</b></summary>

A zero-dependency, single-file web tool for drafting **Dead by Daylight killers** in tournament formats
**BO1 / BO3 / BO5 / BO7 / BO9**. It draws a random killer pool, alternates bans and picks between two teams
(picks are interleaved between bans), and always leaves exactly **one** killer untouched — that killer is
automatically placed at the end of the ordered result table.

- Open [`dist/index.html`](dist/index.html) — works fully offline, no install.
- Source lives in `src/` (classic scripts sharing one scope, so it also runs straight from `file://`).
- `npm run build` inlines everything into the single-file build, `npm test` runs 109 assertions.
- Killer names follow the official/community Chinese naming; legacy names are searchable aliases.

</details>
