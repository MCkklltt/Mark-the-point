# Mark the point

一个面向 **Minecraft 1.20.1 Forge** 的客户端标点（Marker / Ping）模组。按下按键即可在视线命中的方块表面钉上一个始终朝向摄像机的标点，标点正下方实时显示你与它的距离，并支持样式、颜色、透明度、音效与接收者等一整套设置。

* 模组 ID：`markthepoint`
* 支持版本：Minecraft 1.20.1 + Forge 47.x（构建目标 `1.20.1-47.2.0`）
* 环境：客户端功能为主；**服务端可选安装**（安装后标点可在玩家之间同步）
* 软依赖：**TaCZ (Timeless and Classics Zero)** —— 未安装时一切照常工作，绝不崩溃

---

## 1. 功能一览

| 功能 | 说明 |
| --- | --- |
| 放置标点 | 默认按键 **V**，从视线方向做射线检测（默认 128 格，可调），命中方块后在命中点沿法线偏移 **0.05 格**处生成标点，避免 z-fighting |
| 未命中 | 不生成标点，播放一次“失败提示音”（音效可在设置中更换，也可关闭） |
| 提示音 | 放置成功时播放所选的音符盒音效（16 种，用列表选择） |
| 标点渲染 | 世界空间渲染，始终朝向摄像机（billboard），7 种样式可选 |
| **大小恒定** | 标点与距离文字在屏幕上的显示大小**都不随距离变化**；两者各有独立的大小滑块（标点默认 18 px、距离文字默认 10 px） |
| **穿墙可见** | 标点与距离文字**不受任何方块/实体遮挡**（始终绘制在最上层）；**只有开镜（ADS）时**才恢复正常的遮挡关系 |
| 距离文字 | 标点正下方实时显示 `12.34 m`（精确到 0.01 米），文字始终面向玩家，大小恒定且可单独调整 |
| 生命周期 | 默认存活 **30 秒**，客户端计时 + 服务端同步过期 |
| 数量上限 | 同一玩家默认最多 **5 个**，超出时移除最旧的 |
| 设置界面 | 默认按键 **B**，仿钠（Sodium）视频设置的布局：顶部分页 + 整行选项列表 + 悬停高亮 + 悬停提示，按钮与滑块统一为**半透明黑色**扁平样式 |
| 接收者多选 | 列出当前服务器在线玩家（含历史离线玩家），复选多选，全选 / 全不选，默认全选 |
| TaCZ 联动 | 开镜（ADS）时标点透明度按帧插值平滑过渡到“最大透明度”，退出开镜平滑回到“最小透明度” |
| 崩溃防护 | 所有网络、反射、渲染、音效、配置读写均包裹异常处理；单个功能失败只会静默降级，不会导致游戏崩溃 |

---

## 2. 构建

### 2.1 环境要求

* **JDK 17**（Forge 1.20.1 必须使用 Java 17；`build.gradle` 中已声明 toolchain 17）
* 网络可访问 MinecraftForge / Mojang / Maven 仓库
* Gradle 由仓库自带的 wrapper 提供，**无需**单独安装

### 2.2 构建命令

Windows：

```bat
set JAVA_HOME=C:\Program Files\Java\jdk-17
gradlew.bat build
```

Linux / macOS：

```bash
export JAVA_HOME=/path/to/jdk-17
./gradlew build
```

首次构建会下载并反编译 Minecraft（约 5～15 分钟，取决于网络），之后的构建是增量编译。

产物：

```
build/libs/markthepoint-1.0.3.jar
```

仓库中同时提供了一份已经构建好的发行包：`dist/markthepoint-1.0.3.jar`（可直接丢进 `mods/`）。

> **提示**：构建缓存默认放在项目内的 `.gradle-home/`（约 1.3 GB，已被 `.gitignore` 忽略）。
> 它只是缓存，可随时删除；保留它可以让下一次构建不用重新下载并反编译 Minecraft。

> **国内网络提示**：`gradle/wrapper/gradle-wrapper.properties` 中的 `distributionUrl` 默认指向腾讯云镜像
> （`https://mirrors.cloud.tencent.com/gradle/gradle-8.1.1-bin.zip`）。如需改用官方地址，把它换回
> `https://services.gradle.org/distributions/gradle-8.1.1-bin.zip` 即可。

### 2.3 开发环境运行（可选）

```bat
gradlew.bat runClient
```

---

## 3. 安装

1. 安装 **Forge 1.20.1（47.x）**。
2. 把 `markthepoint-1.0.3.jar` 放进 `.minecraft/mods/`。
3. 启动游戏。**单人游戏 / 局域网**：客户端安装即可。
4. **专用服务器**：把同一个 jar 也放进服务端的 `mods/` 才能让标点在玩家之间共享；服务端未安装时，标点只在本地显示（不会报错、不会掉线）。

TaCZ 是**可选**前置：想用开镜联动就把它装在客户端，不装也不会影响任何其它功能。

---

## 4. 按键

| 默认按键 | 功能 | 说明 |
| --- | --- | --- |
| **V** | 放置标点 | 从视线方向做射线检测，命中方块表面即生成标点 |
| **B** | 打开设置界面 | 半透明背景，原版风格 |

两个按键都在原版 **选项 → 控制 → 按键绑定** 中的 **“标点 (Mark the point)”** 分类下，可自由修改。

---

## 5. 设置项说明

设置界面仿照**钠（Sodium）视频设置**的布局：

* 顶部是标题与**分页标签**：`外观` / `行为` / `可见性`
* 中间是可滚动的**整行选项列表**：左侧是选项名，右侧是控件；鼠标悬停时整行高亮并显示说明提示（tooltip）
* 右侧有滚动条，内容比窗口高时才出现
* 所有按钮与滑块都是**半透明黑色**的扁平样式（不再是原版贴图控件），鼠标悬停时略微提亮
* 底部是 `重置` / `保存` / `完成` 按钮，左下角固定显示 **By MCkklltt**

三个按钮：

* **保存** —— 写入 `config/markthepoint-client.json` 并**立即生效**
* **重置** —— 恢复全部默认值（**不会**自动保存，需再点一次“保存”）
* **完成** —— 关闭界面（等价于 ESC，不保存未保存的修改）

界面内容：

| 分页 | 设置项 | 取值范围 / 说明 |
| --- | --- | --- |
| 外观 | **标点样式** | 点击右侧按钮弹出**列表**选择：`经典菱形` / `十字准星` / `圆环（脉冲）` / `向下箭头` / `方框` / `五角星` / `脉冲圆点`；列表右侧有实时预览 |
| 外观 | **标点大小** | 6–64 **GUI 像素**，默认 18 px；标点在屏幕上的大小恒定，不受距离影响 |
| 外观 | **距离文字大小** | 6–48 **GUI 像素**，默认 10 px；距离文字与标点一样恒定大小，并且可以单独调整 |
| 外观 | **标点颜色** | 右侧显示当前颜色，下面是 16 色预设色板 + R / G / B 三个精确滑块（0–255） |
| 外观 | **最大透明度** | 0%–100%，TaCZ 开镜时过渡到的目标透明度 |
| 外观 | **最小透明度** | 0%–100%，日常状态的透明度；**若大于最大透明度会被自动纠正** |
| 行为 | **提示音音效** | 点击名称弹出**列表**（16 种音符盒音效）选择，右侧 **试听** 按钮立即试听 |
| 行为 | **失败音效** | 射线未命中时播放的音效，同样用**列表**选择 + 试听 |
| 行为 | **失败提示音** | 开 / 关，未命中时是否播放上面的失败音效 |
| 行为 | **射线距离** | 8–512 格，默认 128 格 |
| 行为 | **最大标点数量** | 1–20，默认 5；超出时移除最旧的标点 |
| 行为 | **标点存活时间** | 5–300 秒，默认 30 秒（服务端可再下调） |
| 行为 | **TaCZ 开镜联动** | 开 / 关，并显示 TaCZ 的检测状态（未安装 / 已就绪 / API 未找到） |
| 可见性 | **接收者** | 在线玩家多选列表 + 历史离线玩家（标注“离线”），全选 / 全不选，默认全选 |

滚轮方向与原版一致：**向下滚 = 查看下方内容**，向上滚回到顶部。

### 5.1 标点大小与穿墙（重要）

* **大小恒定**：每一帧都用「距离 + 当前投影矩阵」换算出世界尺寸，使其在屏幕上始终占据相同的像素数。
  这样开镜倍率、疾跑 FOV 变化都不会让标点忽大忽小。**标点**与**距离文字**各有一个大小滑块（默认 18 px / 10 px），互不影响。
* **穿墙可见**：平时标点与距离文字会**关闭深度测试**，因此任何方块、实体、粒子都遮不住它；
  当你用 TaCZ **开镜（ADS）**时，会自动恢复正常的深度测试，标点重新被地形遮挡——这是按需求刻意设计的，避免开镜时标点糊在准星上。

### 5.1 透明度语义（重要）

滑块填的是**透明度**（transparency），实际渲染的 **alpha = 1 − 透明度**：

* 日常 alpha = `1 − 最小透明度`（默认 20% → alpha 0.80，清晰可见）
* TaCZ 开镜时 alpha = `1 − 最大透明度`（默认 85% → alpha 0.15，几乎让开视野）

也就是说：“最小透明度”是平时最清晰的状态，“最大透明度”是开镜时最淡的状态。

### 5.2 配置文件

客户端设置：`config/markthepoint-client.json`（保存时写入，UTF-8，格式化 JSON）

```json
{
  "style": "CLASSIC_DIAMOND",
  "markerSizePx": 18.0,
  "labelSizePx": 10.0,
  "color": 4170851,
  "minTransparency": 0.2,
  "maxTransparency": 0.85,
  "soundId": "block.note_block.pling",
  "failSoundId": "block.note_block.bass",
  "playSound": true,
  "failSound": true,
  "maxDistance": 128.0,
  "maxMarks": 5,
  "lifetimeSeconds": 30,
  "taczLink": true,
  "allReceivers": true,
  "receivers": [],
  "knownReceivers": {}
}
```

* `markerSizePx` / `labelSizePx` 是标点 / 距离文字在屏幕上的大小（GUI 像素，6–64 / 6–48）
* `soundId` / `failSoundId` 是放置音效 / 失败音效，取值就是那 16 个音符盒音效 ID
* `color` 是 `0xRRGGBB` 十进制值
* `allReceivers: true` 表示“所有人可见”（此时 `receivers` 被忽略）
* `knownReceivers` 是 `UUID → 玩家名` 映射，用于在离线后仍能在列表中显示该玩家
* 文件被手改坏也不会崩：读取时会做完整校验（越界值、非法 UUID、非法音效名都会被纠正）

服务端（通用）设置：`config/markthepoint-common.toml`

```toml
[marks]
	# Lifetime in seconds used when a client does not request one.
	markLifetimeSeconds = 30
	# Hard upper bound for a mark lifetime in seconds requested by a client.
	maxMarkLifetimeSeconds = 300
	# Hard cap of simultaneously alive marks per player. Older marks are dropped first.
	maxMarksPerPlayer = 32
	# Sanity check: maximum distance in blocks between the shooter's eye and the mark.
	maxPlaceDistance = 512.0
```

---

## 6. TaCZ 联动说明

### 6.1 前置条件

* 客户端安装 **TaCZ (Timeless and Classics Zero)** 1.20.1 版本（模组 ID `tacz`）。
* `mods.toml` 中 TaCZ 被声明为 **`mandatory=false`** 的可选依赖：缺失时 Forge 不会报错，模组照常加载。

### 6.2 判定方式（全程反射，零硬依赖）

```java
// 仅在 ModList.get().isLoaded("tacz") 为 true 时才尝试
Class<?> c = Class.forName("com.tacz.guns.api.client.gameplay.IClientPlayerGunOperator");
Method fromLocalPlayer = c.getMethod("fromLocalPlayer", LocalPlayer.class);
Method isAim          = c.getMethod("isAim");
Object operator = fromLocalPlayer.invoke(null, player);
boolean aiming  = (Boolean) isAim.invoke(operator);
```

* 方法查找使用“按名字 + 参数可赋值 + 返回 boolean”的宽松匹配，能容忍 TaCZ 小版本的签名差异。
* 整个流程用 `try/catch` 包裹：**反射失败 = 静默降级为不联动**，日志中最多提示一次。
* 连续失败超过 20 次后本局不再尝试，避免每帧抛异常的额外开销。
* 未安装 TaCZ（或未开启联动）时，标点透明度**恒为最小透明度**。

### 6.3 过渡曲线

每帧（不是每 tick）用基于真实帧间隔的指数插值：

```java
current += (target - current) * Math.min(1.0F, deltaTime * 8.0F);
// deltaTime = Minecraft.getInstance().getDeltaFrameTime()（秒），并 clamp 到 [0, 0.25]
```

因此开镜 / 退出开镜都是平滑淡入淡出，**不会瞬间跳变**，高帧率与低帧率下手感一致。

---

## 7. 多人 / 网络行为

* 放置标点时客户端会把“位置 + 存活时间 + 接收者列表”发给服务端；服务端做合法性校验（距离上限、时间上限、每人数量上限）后，只转发给同维度且在选择范围内的玩家。
* 标点 ID、过期时间以**服务端为准**；客户端自身也保存一份副本用于渲染，并在本地计时到期后自行移除（服务端同时会下发移除包）。
* 服务端没有安装本模组（或握手时没有本模组的通道）时，`isRemotePresent` 判定为 false，标点退化为**仅本地可见**，不会报错、不会踢出。
* 空接收者列表在协议中表示“同维度所有人”；**放置者本人始终能看到自己的标点**（即使把自己从列表里去掉）。

---

## 8. 已知限制

1. **只有方块表面可以放置**：射线只做方块碰撞检测（`ClipContext.Block.COLLIDER`），不检测实体；命中实体不会生成标点。
2. **服务器需装模组才能共享**：只装客户端时标点仅自己可见（这是设计上的安全降级）。
3. **服务端上限优先**：`config/markthepoint-common.toml` 中的 `maxMarksPerPlayer` / `maxMarkLifetimeSeconds` / `maxPlaceDistance` 是硬上限，客户端设置超过时会被服务端裁剪。
4. **标点不跨维度**：切换维度后，其它维度的标点不会被渲染（同一维度内正常）。
5. **距离按眼睛位置计算**：显示的是“眼睛 → 标点”的直线距离，不是贴地行走距离。
6. **透明度语义**：见 5.1，滑块是“透明度”而不是“不透明度”。
7. **TaCZ 联动依赖其客户端 API**：若未来 TaCZ 大版本改了包名 / 方法名，联动会自动停用（不会崩溃），此时可在设置界面看到“TaCZ API 未找到”。
8. **首次构建较慢**：ForgeGradle 需要下载并反编译 Minecraft。
9. 标点仅存在于当前会话：退出游戏 / 重启服务器后不会保留（本模组不做持久化）。
10. **穿墙是“永远在最上层”**：标点会盖在其他模组的 HUD/粒子之上（只要在 3D 场景里），这是需求要求的；开镜时会恢复正常遮挡。
11. **大小以 GUI 像素计**：改「视频设置 → GUI 缩放」时，同一个数值在不同 GUI 缩放下占用的物理像素数不同，但相对其他界面元素的大小一致。
12. **距离文字有独立滑块**：默认 10 px，范围 6–48 px；它和标点一样恒定大小，但不跟随标点大小联动（可以单独调小/调大）。

---

## 9. 项目结构

```
src/main/java/com/markthepoint/
├── MarkThePoint.java                 模组入口（注册配置、网络）
├── CommonConfig.java                 服务端通用配置 markthepoint-common.toml
├── network/                          SimpleChannel 与四个数据包
│   ├── MtpNetwork.java
│   ├── ServerboundPlaceMarkPacket.java    客户端 → 服务端：放标点
│   ├── ServerboundRemoveMarkPacket.java   客户端 → 服务端：撤标点
│   ├── ClientboundAddMarkPacket.java      服务端 → 客户端：新增标点
│   └── ClientboundRemoveMarkPacket.java   服务端 → 客户端：移除标点
├── server/ServerMarkManager.java     服务端权威标点表（上限 / 过期 / 广播）
└── client/
    ├── ClientConfig.java             config/markthepoint-client.json 读写与校验
    ├── MarkStyle.java / MarkSound.java    7 种样式 / 16 种音符盒音效
    ├── MtpKeyMappings.java           V / B 按键注册
    ├── ClientEvents.java             tick 中处理按键、断线清理
    ├── MarkPlacer.java               射线检测 + 法线偏移 + 发包
    ├── ClientMark.java / ClientMarkStore.java   客户端标点表与本地上限
    ├── ClientNetworking.java         服务端是否有本模组的判定
    ├── ClientPacketHandler.java      客户端收包处理
    ├── TaczCompat.java               TaCZ 反射桥（软依赖）
    ├── MarkAlpha.java                基于 deltaTime 的平滑 alpha 插值
    ├── MtpSounds.java                音符盒音效播放
    ├── MarkShapeRenderer.java        7 种形状的几何绘制（世界 + 设置界面预览共用）
    ├── MarkRenderer.java             billboard + 恒定屏幕大小 + 穿墙 + 距离文字
    └── gui/
        ├── MarkSettingsScreen.java   设置界面（分页 + 整行选项列表，钠风格）
        ├── ListPopup.java            弹出式可滚动选项列表（样式 / 音效选择）
        ├── FlatButton.java           半透明黑色扁平按钮
        ├── FlatSlider.java           半透明黑色扁平滑块
        └── FlatWidgets.java          扁平控件共用的绘制工具
```

语言文件：`src/main/resources/assets/markthepoint/lang/en_us.json` 与 `zh_cn.json`。

---

## 10. 构建与运行验证

本次交付的 jar 已经实际构建并做过启动验证：

| 验证项 | 结果 |
| --- | --- |
| `gradlew build`（ForgeGradle 6 / Gradle 8.1.1 / JDK 17） | ✅ BUILD SUCCESSFUL |
| 重混淆（reobf）正确性 | ✅ 类引用的方法/字段已变为 SRG 名（如 `Level.m_46472_`、`BlockGetter.m_45547_`） |
| `mods.toml` 占位符展开 | ✅ `modId/version/loaderVersion/dependencies` 等均已正确替换 |
| **专用服务器**启动（`gradlew runServer`） | ✅ `Done (15.707s)!`，加载 `com.markthepoint.MarkThePoint`，生成 `config/markthepoint-common.toml`，`ServerMarkManager` 已订阅事件，无异常 |
| **开发客户端**启动（`gradlew runClient`） | ✅ 资源重载完成、`Sound engine started`；`ClientEvents` / `MarkRenderer` 订阅到 FORGE 总线，`MtpKeyMappings` 注册到 MOD 总线，无异常 |
| 语言文件 | ✅ `en_us.json` / `zh_cn.json` 均为合法 JSON，键完全一致（各 84 条），客户端重载时无报错 |
| **多标点渲染 + 新设置界面（自动化自测，1.0.3）** | ✅ 真实客户端进入世界，同一时刻 **4 个标点** 连续渲染；打开设置界面 → 切换三个分页 → 打开样式/音效/失败音效三个**列表弹层**并用真实点击选中 → 滚动 → 保存 → 关闭，全流程**无任何异常** |
| 列表选择生效 | ✅ 自测点击列表后状态变为 `RING`、`block.note_block.hat`（成功音效与失败音效分别可改） |
| 两套大小设置 | ✅ 自测状态输出 `marker=18px | label=10px`，标点与距离文字各自独立 |
| 滚轮方向 | ✅ 自测记录 `before=0 → 向下滚=18 → 向上滚=0`，与原版一致 |

日志片段（自测，真实客户端，1.0.3）：

```
[selftest] ray cast placement (key path)
[selftest] added mark #… (x4)
[selftest] appearance tab + style list pick: RING | block.note_block.pling | block.note_block.bass | marker=18px | label=10px
[selftest] behaviour tab + both sound lists: RING | block.note_block.hat | block.note_block.hat | marker=18px | label=10px
[selftest] visibility tab, rows rendered, scroll=0
[selftest] scroll before=0 wheelDown=18 wheelUp=0 (down must be larger)
[selftest] saved: RING | block.note_block.hat | block.note_block.hat | marker=18px | label=10px
[selftest] screen closed
[selftest] COMPLETE marks=4
```

尚未验证（需要人工交互 / 真实环境）：

* 人工按 **V** / **B** 的实际手感测试（自测里射线放置、界面与列表点击路径均已执行过）；
* “大小恒定 / 穿墙可见”在**肉眼**上的效果（代码路径已在自测中连续运行，但视觉观感需要你自己看一眼）；
* 与真实 TaCZ 的联调（需要另行安装 TaCZ 并实际开镜，验证 ADS 时的淡出与遮挡恢复）；
* 多人环境下接收者过滤的实际联机测试。

---

## 11. 更新记录

### 1.0.3

* **新增：距离文字也像标点一样。** 现在距离文字有独立的「距离文字大小」滑块（6–48 GUI 像素，默认 10 px），
  与标点一样按「距离 + 投影矩阵」换算世界尺寸，**远近恒定大小**、同样**穿墙可见**、开镜时同样恢复遮挡。
  之前它是按标点大小的 50% 联动，现在两者互不影响。
* **改动：设置界面的按钮与滑块改成半透明黑色扁平样式。**
  底部的 `重置` / `保存` / `完成`、开关按钮、列表按钮、以及全部滑块都不再使用原版贴图控件，
  统一为半透明黑底 + 细边框 + 悬停提亮，与钠风格的列表更协调（新增 `FlatButton` / `FlatSlider` / `FlatWidgets`）。

### 1.0.2

* **新增：标点在屏幕上的显示大小恒定。** 每帧按「距离 + 当前投影矩阵」反算世界尺寸，因此无论远近、开镜倍率、疾跑 FOV 变化，
  标点占据的屏幕像素数都不变；新增设置项「标点大小」（6–64 GUI 像素，默认 18），距离文字高度按标点大小的 50% 联动。
* **新增：标点与距离文字穿墙可见。** 平时关闭深度测试，任何方块/实体都遮不住；**只有在 TaCZ 开镜（ADS）时**才恢复正常的遮挡关系。
* **重做：设置界面改为钠（Sodium）视频设置的布局。**
  顶部分页（外观 / 行为 / 可见性）+ 整行选项列表（左名右控件、悬停高亮、悬停说明）+ 右侧滚动条 + 底栏按钮与署名。
* **新增：失败音效可修改。** 新增 `failSoundId` 配置项与对应的设置行。
* **改动：音效与样式的选择方式改为列表。** 点击控件弹出可滚动列表（最多 8 行，超出可滚动），点击条目即可选中，
  当前值高亮显示；音效行右侧保留「试听」按钮。

### 1.0.1

* **修复：连续放置第二个标点会导致渲染崩溃。**
  标点下方的距离文字被写进了与标点形状**共用的** `Tesselator` 缓冲区，文字批次尚未提交时第二个标点又去 `begin()`，于是抛出
  `IllegalStateException: Already building!`；共享缓冲区从此卡死，随后连原版准星 / 暗角等界面渲染也一起报错。
  现在标点形状改用**私有的** `BufferBuilder`，文字改用独立的 `minecraft.renderBuffers().bufferSource()`，并改为
  “先画完所有形状，再统一画所有文字”；任何一次绘制失败都会在 `finally` 中释放缓冲区并还原渲染状态，不可能再污染后续渲染。
* **修复：设置界面滚轮方向相反。** 现在向下滚查看下方内容、向上滚回到顶部，与原版一致。
* **新增：设置界面最下方固定显示 `By MCkklltt`。**

### 1.0.0

* 首个版本：V 放置标点、B 打开设置、7 种样式、颜色、双透明度滑块、16 种音符盒音效、接收者多选、TaCZ 开镜平滑联动。

---

## 12. 常见问题


**Q：按 V 没有反应？**
A：确认按键绑定里“放置标点”仍是 V，且没有被其它模组占用；准星必须指向方块（不是天空 / 实体）。

**Q：标点看不见？**
A：检查“最小透明度”是不是拉得太高（100% = 完全不可见），“射线距离”是否足够远；标点默认 30 秒后消失。

**Q：开镜时标点没有变淡？**
A：确认已安装 TaCZ、设置里的“TaCZ 开镜联动”为开，且按钮后面显示“（TaCZ 已就绪）”。显示“（TaCZ API 未找到）”说明该 TaCZ 版本的类名 / 方法名不同。

**Q：多人游戏里队友看不到我的标点？**
A：服务端也需要安装本模组；同时确认“可见性”列表里勾选了对方。

---

## 13. 许可

MIT。模组图标由 MCkklltt 提供。
