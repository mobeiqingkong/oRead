<div align="center">

# 离阅 · oRead

**纯离线本地阅读器 · 六种格式 · 中英双语**

`v1.0.0 (versionCode 42)`　·　`com.book.offlineReader`　·　无网络权限

</div>

---

## 这是什么

「离阅」(oRead) 是一款**完全离线**的本地阅读器：把书放进手机，它负责排版、翻页、朗读、换肤。

这里提供的是它的**增强改造版**。相比原版，做了两件事：

1. **补齐了三种主流格式** —— 原版只能读 TXT / UMD / EPUB，现在 **PDF、MOBI、ZIP 漫画包**也能直接翻开。
2. **重做了翻页与滚动的手感** —— 目标只有一个：**快，而且丝滑。**

**TXT、UMD、EPUB、PDF、MOBI、ZIP —— 六种文件，一个应用读完。** 界面支持跟随系统 / 简体中文 / English。

---

## 📚 支持的文件格式

| 格式 | 扩展名 | | 说明 |
|:---|:---|:---:|:---|
| **TXT** | `.txt` | 原生 | 纯文本小说，自动分章、断句、排版，超大文件也流畅 |
| **UMD** | `.umd` | 原生 | 手机电子书格式，章节与插图解析完整 |
| **EPUB** | `.epub` | 原生 | 标准电子书格式 |
| **PDF** | `.pdf` | ⭐ 新增 | 扫描版、图文版都能读；**支持全文搜索**；翻页 / 上下滚动两种阅读方式 |
| **MOBI** | `.mobi` `.prc` `.azw` `.azw3` | ⭐ 新增 | Kindle 系格式，自研解析器，图片页按需解码 |
| **ZIP 漫画** | `.zip` | ⭐ 新增 | 图片压缩包直接当漫画翻开，自动识别是否为图片包 |

### 几个细节

- **自动出现在「打开方式」里** —— 从文件管理器、浏览器、聊天软件拿到的书，长按选「用其他应用打开」，就能看到「离阅」。
- **书架格式角标** —— 书架切到详情视图时，封面右上角显示格式角标，一眼分辨：**T**XT / **U**MD / **E**PUB / **P**DF / **M**OBI / **Z**IP。
- **ZIP 只认图片包** —— 应用本身自带压缩包浏览功能，所以只有当一个 zip 里确实是一堆图片时，才按漫画打开；装着一包 txt / epub 的压缩包仍走原来的逻辑，两者互不干扰。
- **六种格式一视同仁** —— 都能自动提取封面缩略图、记录阅读进度、出现在「最近阅读」里，也都共用同一套阅读外壳（目录书签、亮度、夜间模式、简繁转换……）。
- **零第三方渲染库** —— PDF 走 Android 系统自带的 `PdfRenderer`，MOBI 与 ZIP 为自研解析，APK 体积不因此膨胀。

---

## ⚡ 快，而且丝滑

> 这一版的翻页和滚动，是照着手感重写过的。

### 翻页：零等待

当前页渲染完成时，**相邻页已经在后台解码并缓存好了**。翻过去看到的是成品，不会出现「先白屏、再逐行冒出来」的空窗。

### 滚动：有惯性

手指一划，内容会**按你的手速继续滑行，再自然减速停下** —— 和 TXT 阅读器完全同一套手感，不是「拖多少走多少」。

### 大图不卡、不崩

漫画单页动辄 2000×3000 像素，一张原图就是 24 MB。这里做了三件事：

- 解码按屏幕宽度**按需降采样**，不把原图整张读进内存；
- 页缓存**窗口钉住** —— 正在显示的页永远不会被回收，杜绝「加载中」反复闪现；
- 缓存同时受**张数上限**与**内存预算**两道约束，几千页的漫画包也不会 OOM。

### 画面不闪、底色不跳

滚动时精确定位可见页范围，不多要一页；翻页动画的底板颜色与阅读底色一致，动画过程中不会闪出一块突兀的色块。

### 实测（模拟器 1080×2400，打开 197 页 ZIP 漫画）

| 指标 | 优化前 | 优化后 |
|:---|---:|---:|
| 页缓存抖动（静止观察 18 秒） | 反复淘汰 **188 次**，画面不停闪烁 | **不再抖动**，画面连续 6 帧完全一致 |
| 内存 Native Heap | 67 MB | **35 MB** |
| 进程 TOTAL Pss | 101 MB | **49 MB** |
| 静止后 GC 活动 | 每秒 1~2 次（单次暂停最高 367 ms） | **基本停止** |

---

## ⚠️ 不需要任何网络权限

这是本应用最重要的特性，也是它区别于绝大多数「免费小说 App」的地方：

> 应用清单里 **未声明 `android.permission.INTERNET`**。

这不是一句「承诺不联网」，而是**技术上根本连不出去**：没有这个权限，进程连一个 socket 都建不起来。所以它不可能上传你的阅读记录、书架内容、设备信息，也不可能加载联网广告或做任何云端同步。所有数据（书库、阅读进度、书签、皮肤、设置）只存在你自己的手机上。

### 完整权限清单

| 权限 | 用途 |
|---|---|
| `READ_EXTERNAL_STORAGE` | 扫描并读取本机电子书 / 漫画文件 |
| `WRITE_EXTERNAL_STORAGE` | 导入书籍、保存书架数据与备份文件 |
| `WAKE_LOCK` | 长时间阅读 / 朗读时保持唤醒，不中途熄屏 |
| `VIBRATE` | 翻页与操作的震动反馈 |
| `FOREGROUND_SERVICE` | 朗读（TTS）时在前台持续播放 |
| `POST_NOTIFICATIONS` | 朗读控制与后台状态通知 |

**没有网络相关权限，也没有读取通讯录、位置、相机、麦克风等任何权限。**

---

## 截图

### 六种格式，同一套阅读体验

| TXT 文本 | PDF |
|:---:|:---:|
| ![TXT](images/08-format-txt.jpg) | ![PDF](images/09-format-pdf.jpg) |

| ZIP 漫画包 | MOBI 漫画 |
|:---:|:---:|
| ![ZIP 漫画](images/10-format-zip.jpg) | ![MOBI](images/11-format-mobi.jpg) |

上面分别是 TXT 正文页、PDF 扫描页、ZIP 漫画包、MOBI 漫画。四种格式共用同一套阅读外壳 —— 顶栏是 返回 / 亮度 / 简繁转换 / 搜索 / 书签，底栏是 **偏好、跳转、目录书签、视角**，底部常驻显示页码、时间与阅读进度百分比。

### 阅读页

| 划词选中 | 阅读工具栏 |
|:---:|:---:|
| ![划词选中](images/01-reading-select.jpg) | ![阅读工具栏](images/02-reading-toolbar.jpg) |

阅读页支持长按划词选中；点击屏幕中央呼出工具栏（英文分别为 Style / Options / Go to / Contents / View / Auto / Night / Read）。

### 设置页

| 中文界面 | English |
|:---:|:---:|
| ![系统设置-中文](images/04-settings-zh.jpg) | ![Settings-英文](images/03-settings-en.jpg) |

设置分「系统设置 / 阅读设置 / 关于」三个页签（英文为 System / Reading / About），顶部标题栏随当前页签切换。

### 书架与阅读辅助

| 书架分类 | 书架管理菜单 | 阅读辅助（皮肤） |
|:---:|:---:|:---:|
| ![书架分类](images/05-bookshelf-categories.jpg) | ![书架菜单](images/06-bookshelf-menu.jpg) | ![阅读辅助](images/07-reading-assistant.jpg) |

---

## 功能一览

### 📖 阅读

- **六种格式**：TXT / UMD / EPUB / PDF / MOBI / ZIP 漫画包，各有专门的渲染路径
- **翻页与滚动**：每种格式都支持「翻页」与「上下滚动」两种阅读方式，带惯性滑动、多种翻页动画、横竖屏、全屏阅读
- **超大文件**：文本自动分章排版；漫画按需解码，几千页也不卡
- **PDF 全文搜索**：搜索面板与文本阅读器保持一致，结果列出命中页的百分比与摘录，命中词高亮
- **目录与书签**：跳章、加书签、全书搜索；PDF / MOBI / ZIP 同样支持目录与书签
- **划词选中**：长按选段，便于摘录与复制
- **简繁转换**：顶栏「简」提供 繁体→简体 / 简体→繁体 的强制转换（转换会改变书籍文件，界面内附有耗时警告，建议谨慎使用）
- **夜间模式**：一键切换昼夜配色，配合亮度调节长时间护眼
- **朗读（TTS）**：调用系统语音引擎朗读，前台服务持续播放，通知栏可控
- **线控与音量键**：耳机线控、音量键均可设为翻页
- **休息提醒**：可设阅读计时提醒，避免长时间用眼

### 📚 书库

- **智能导入**：批量扫描本机目录导入书籍，自动识别格式
- **格式角标**：详情视图下封面右上角标注 T / U / E / P / M / Z，一眼分辨
- **书架分类**：最近阅读、文学名著、历史名著、灵异恐怖、推理悬疑、武侠玄幻、学习应用、幽默搞笑、杂书、其它、未知作品、临时…… 可自定义归类
- **书架管理**：搜索书架、整理归类、清空书架
- **进度记忆**：每本书独立记录阅读进度与最后阅读时间，六种格式均支持
- **封面缩略图**：自动提取封面，PDF / MOBI / ZIP 同样适用
- **备份 / 恢复**：一键备份与还原书架与设置数据
- **应用密码**：给应用加锁，防止他人翻看
- **硬件加速开关**：按机型需要开启或关闭 GPU 加速绘制

### 🎨 外观

- **阅读辅助**：分「皮肤 / 字体 / 主题」三个页签
- **内置多套皮肤**：默认皮肤、书香古木、仿 Anyview 风格、仿古木、粽叶飘香、八三男人节等
- **字体与主题**：可换字体、字号、行距与主题配色

### 🌐 双语界面

- 界面语言支持 **跟随系统 / 简体中文 / English**
- 切换后自动重启应用即刻生效
- 英文界面下，底部工具栏、设置页签、阅读设置面板等窄栏位文案均已按宽度适配，不会截断或换行

---

## 安装

1. 下载 [`oRead-1.0.0-bilingual.apk`](oRead-1.0.0-bilingual.apk)
2. 在手机上直接安装（首次需允许「安装未知来源应用」）
3. 若手机已安装**官方原版**或**本项目的旧版本**，请先卸载再安装 —— 本包使用自签名证书，与原版签名不同，无法覆盖安装
   > 卸载会清空原有书架数据，建议先在原版内做一次「备份」并把备份文件拷出

APK 大小约 **33.4 MB**（35,076,372 字节）

<details>
<summary>校验和（SHA-256）</summary>

```
382955cc196669bd068ff71a914f8352cd9205694e8731e2dbb5ae1a0ee6c6f7  oRead-1.0.0-bilingual.apk
```

</details>

---

## 兼容性

| 项 | 值 |
|---|---|
| 包名 | `com.book.offlineReader` |
| 版本 | 1.0.0（versionCode 42） |
| 应用名 | 离阅（英文 oRead） |
| 最低支持 | API 5（Android 2.0） |
| 目标版本 | API 28（Android 9） |
| 应用内语言 | 跟随系统 / 简体中文 / English |
| PDF 阅读 | 需 **Android 5.0（API 21）** 及以上；其余五种格式不受此限 |

---

## 本版做了什么

### 一、新增三种格式

| # | 项 | 处理 |
|---|---|---|
| 1 | **PDF** | 接入系统原生 `PdfRenderer` 渲染，扫描版 / 图文版均可读，支持翻页与滚动两种方式、全文搜索（结果带命中页百分比与关键词高亮）。**零第三方库，APK 体积不因此膨胀** |
| 2 | **MOBI** | 自研解析器，支持 `.mobi` `.prc` `.azw` `.azw3`；正文与图片页分离，图片页按需解码 |
| 3 | **ZIP 漫画** | 图片压缩包直接按漫画阅读；自动判定「是否为图片包」，避免抢占应用原有的压缩包浏览功能 |
| 4 | **格式识别** | 上述格式纳入可读白名单，并注册进系统「打开方式」 |
| 5 | **书架角标** | 详情视图封面右上角显示 P / M / Z 角标，与原有 T / E / U 统一为浅蓝底白字 |

### 二、手感与性能

| # | 问题 | 原因 | 处理 |
|---|---|---|---|
| 6 | 漫画画面持续闪烁 | 可见页范围多算了一页，而页缓存上限只有 3 张 —— 多请求的那页恰好把**正在显示的一页**挤掉，形成「解码 → 淘汰 → 再解码」死循环 | 修正可见页范围；新增统一页缓存（30 张上限 + 窗口钉住 + 96 MB 预算） |
| 7 | 滚动没有惯性 | 松手后只按固定系数跳一段，没有衰减过程 | 改用系统标准惯性滚动算法（速度追踪 + 衰减曲线） |
| 8 | 拖动过程中重复写盘 | 同一页内的高频帧也在反复保存进度 | 进度回调节流（100 ms），页码真正变化时仍即时上报 |
| 9 | 翻页动画期间底色突变 | 动画底板写死深灰，与纯黑底色的漫画阅读器不一致 | 底板颜色改为跟随阅读器底色 |

### 三、双语界面（v1.0.0 的修复）

| # | 问题 | 原因 | 处理 |
|---|---|---|---|
| 10 | 语言项默认显示 English，本机是中文却不跟随 | 语言取值的判断分支写反了 | 修正判断逻辑，未设置时按「跟随系统」处理 |
| 11 | 点击「语言」不弹窗、选完没反应 | 语言项用了自定义的列表对话框，它顶掉了系统的选项回调，选择结果传不出来 | 改回框架标准单选对话框，回调链恢复 |
| 12 | 切换语言后应用直接退出，而不是像换肤那样重启 | 启动新实例后**立即**杀进程，系统还没处理完启动请求，任务被一起销毁 | 改为延迟重启：先把启动意图交给系统闹钟托管，再退出进程，约 0.3 秒后由系统以全新进程拉起 |
| 13 | 切到英文后多处文字不显示或挤成 `...` | 英文标签过长，而设置页页签只有屏宽 1/3、底栏格子仅 64dp、阅读设置面板仅 90dp | 为窄栏位改用简短英文（如 Preferences → Options、Contents & Bookmarks → Contents） |
| 14 | 设置列表长标题被硬裁掉、摘要把行撑高 | 标题为单行且宽度按内容测量后被父容器裁切；摘要未限行数 | 标题与摘要均改为最多两行并带省略号 |

---

## 免责声明

- 本改造版仅用于**个人学习与逆向研究**，请于下载后 24 小时内自行删除，**请勿用于商业分发**。
- 应用本身的著作权归原作者所有，本仓库不对原应用主张任何权利。
- 使用本应用时请遵守当地法律法规，支持正版内容。

---

<details>
<summary><b>English</b></summary>

### oRead — an offline-only local reader with six formats and a buttery-smooth feel

**No network permission.** The app does not declare `android.permission.INTERNET`. This is not a policy promise — it is a technical impossibility: without that permission the process cannot open a socket at all, so it can never upload your library, reading history or device information, and can never load online ads.

**Supported formats** — TXT · UMD · EPUB · PDF · MOBI (incl. `.prc` / `.azw` / `.azw3`) · ZIP comic archives. PDF, MOBI and ZIP are new in this build. Every format gets automatic cover thumbnails, per-book reading progress and its own entry in the system "Open with" list. PDF rendering uses the platform `PdfRenderer` — no third-party library, so the APK does not grow.

**Fast and smooth** — adjacent pages are decoded and cached ahead of time, so a page turn shows a finished image instead of a blank flash. Scrolling has real inertia: flick and the content keeps gliding, then eases to a stop. Bitmaps are decoded downsampled and the page cache is pinned to the visible window, so thousand-page comic archives stay responsive and never run out of memory. On a 1080×2400 emulator with a 197-page ZIP comic, cache evictions dropped from 188 to 0, native heap from 67 MB to 35 MB, and total Pss from 101 MB to 49 MB; GC stops when the screen is idle.

- **Reading** — six formats, page-turn and continuous-scroll modes, inertial scrolling, highlight/select, simplified↔traditional toggle, TOC & bookmarks, full-text search (PDF included), multiple page-turn animations, night mode, background TTS, headset/wired controls, eye-rest reminders.
- **Library** — bulk smart import, format badges (T/U/E/P/M/Z), category shelves, shelf search & management, per-book reading progress, automatic cover thumbnails, one-tap backup/restore, app password, GPU acceleration toggle.
- **Appearance** — multiple built-in skins, fonts and themes.
- **Language** — Follow system / 简体中文 / English, applied after an automatic restart.

Install: grab `oRead-1.0.0-bilingual.apk`. Uninstall the official build (or any earlier build of this project) first — this package is self-signed and cannot overwrite them.

PDF reading requires Android 5.0 (API 21) or later; the other five formats do not.

For personal study and reverse-engineering research only. All rights to the original application belong to its original authors.

</details>
