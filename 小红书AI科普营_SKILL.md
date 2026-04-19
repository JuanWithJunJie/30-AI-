---
name: xiaohongshu-ai-campaign
description: 生产面向制造业员工的小红书格式 AI 术语科普图文。每期输出 7 张竖版卡片（封面→管理层故事→工程师故事→概念解释→实战场景→误区技巧→总结互动），带品牌 Logo 注入、Playwright 高清截图、严格事实核查。触发条件：用户要求制作「AI科普」「小红书卡片」「AI术语」「Day XX」内容，或任何面向非技术员工的 AI 知识科普图文任务。
license: Internal · 传音制造中心 AI赋能小组
---

# 小红书 AI 术语科普营 · 生产 Skill

本 Skill 封装了「30天AI术语科普营」的完整内容生产方法论，可复用于任何类似的企业内部 AI 科普图文项目。

---

## 一、项目定位与受众分层

### 核心原则
一份内容，三种人群，三种叙事角度——同一期图文必须同时服务三类读者，任何一类都不能被"说不上话"。

### 三类受众

| 受众 | 角色 | 关注点 | 内容风格 |
|------|------|--------|----------|
| 👔 管理层 | CEO / 主管 / IT负责人 | ROI · 风险 · 决策依据 · 业务影响 | 故事化、数据化、结论优先 |
| 🔧 工程师 | AI工程师 / MES工程师 / 算法工程师 | 技术原理 · 架构 · 实现细节 · 代码 | 深夜深色卡风格、技术严谨 |
| 📋 品质/IE | 品质工程师 / IE / 操作员 | 怎么用 · 场景 · 流程 · 避坑 | 通俗、实操、问题导向 |

---

## 二、固定七段式结构（每期必须严格遵守）

每期 7 张卡片，顺序固定，不可删减：

```
卡片1: 封面（Cover）
卡片2: 管理层故事（决策视角）
卡片3: 工程师故事（技术视角，深色背景）
卡片4: 概念解释（类比通俗化，浅色背景）
卡片5: 实战场景（3个制造业具体场景）
卡片6: 误区与技巧（避坑 + 选型指南，深色背景）
卡片7: 总结互动（一句话总结 + 互动问题 + 下期预告）
```

### 各卡片要点

**卡片1·封面**
- 爆款标题：用工厂场景的「反常识发现」或「数字冲击」钩住读者，结尾必须有 👇
- 本期术语（大字）+ 英文注释（小字 Courier New）
- 定制 SVG 插图（体现本期概念的独特可视化）
- 受众标签（👔管理层 🔧工程师 📋品质/IE）
- Day 序号 + 周次标签
- 底部渐变色条

**卡片2·管理层故事**
- 浅色背景（白色或近白）
- 用具体人物（老李/老陈）+ 决策场景开篇
- 必含：量化结果数据（良率提升X% / 效率提高X倍）
- 以引用框结尾（模拟当事人说的话 + 职位署名）

**卡片3·工程师故事**
- 深色背景（近黑色，与第2张对比强烈）
- PCB/网格/扫描线等技术感背景纹理
- 含：技术架构图 / 代码风格代码块 / 对比表 / 性能柱状图
- 数据准确性要求最高

**卡片4·概念解释**
- 浅色背景（与第3张对比）
- 必须有一个「日常生活类比」（医生/图书馆/厨师/瑞士军刀等）
- 类比之后才给技术解释，顺序不可颠倒
- 可含对比表格或选型矩阵

**卡片5·实战场景**
- 3个具体的制造业场景，颜色区分
- 每个场景：职能角色标签 → 标题 → 问题描述 → 解决方案 → 量化结果
- 公司/企业描述必须匿名（「某头部手机制造企业」而非具体公司名）

**卡片6·误区与技巧**
- 深色背景
- 至少 2 个 ❌ 误区（红色 mistake-box）
- 至少 1 个 ✅ 正确指南（绿色/青色 guide-box）
- 可含快速选型表或决策口诀

**卡片7·总结互动**
- 浅色背景
- 一句话总结框（深色背景反白字，突出）
- 互动问题框（让读者在评论区回答）
- 下期预告（带闪烁动态点指示器）
- 底部渐变色条（与封面一致）

---

## 三、视觉设计规范

### 必读 Skill
**每次生成新一期卡片前，必须先读取：**
```
/mnt/skills/user/frontend-design/SKILL.md
```

### 每期独立配色原则
- 30期内配色不重复（已用配色见附录）
- 每期确定一个「主题色调」，深色卡 + 浅色卡交替
- 封面配色决定整期视觉基调
- 避免：紫色渐变+白底（泛滥的AI美学）

### 封面 SVG 插图要求
每期必须设计独特的 SVG 图形，例如：
- 展示概念的可视化图表（对比图、流程图、进化图）
- 工业感电路/芯片/波形图
- 数据可视化（行星大小、条形图等）
- 对话气泡/操作界面模拟

### 字体
- 中文：Noto Sans SC（Google Fonts，必须 @import）
- 英文等宽：Courier New（技术标注用）
- 卡片宽度：390px（小红书竖版标准）

### Logo 规范
```
位置：position:absolute; top:8px; right:14px; z-index:20
尺寸：height:34px; width:auto
深色卡：card-logo--white（白色logo，opacity:.90）
浅色卡：card-logo--black（黑色logo，opacity:.65）
```

---

## 四、技术生产流程（标准 SOP）

### 步骤 1：读取 Skill
```python
# 每次生产前必执行
view('/mnt/skills/user/frontend-design/SKILL.md')
view('/mnt/skills/user/xiaohongshu-ai-campaign/SKILL.md')
```

### 步骤 2：美学设计决策
在编写 HTML 之前，先确定：
- 本期主题色（必须与前几期不同）
- 封面 SVG 插图主题
- 整体美学方向（工业/宇宙/医疗/电路等）
- 记忆点（这张封面让人记住的核心元素）

### 步骤 3：编写 HTML 模板
- 文件写入：`/home/claude/dayXX_template.html`
- 使用 Logo 占位符（不直接注入 base64）：
  ```html
  <!-- 深色卡 -->
    LOGO_WHITE_PLACEHOLDER
  <!-- 浅色卡 -->
    LOGO_BLACK_PLACEHOLDER
  ```

### 步骤 4：Logo 注入
```python
with open('/home/claude/logo_final_white_b64.txt') as f: B64W = f.read().strip()
with open('/home/claude/logo_final_black_b64.txt') as f: B64B = f.read().strip()

LW = f'  <div class="card-logo card-logo--white">\n    <img src="data:image/png;base64,{B64W}" alt="制造中心">\n  </div>'
LB = f'  <div class="card-logo card-logo--black">\n    <img src="data:image/png;base64,{B64B}" alt="制造中心">\n  </div>'

html = html.replace('  LOGO_WHITE_PLACEHOLDER', LW)
html = html.replace('  LOGO_BLACK_PLACEHOLDER', LB)
```

> ⚠️ 如果 logo_final_*_b64.txt 文件不存在，需先从源文件重新生成：
> ```python
> import base64
> with open('/mnt/user-data/uploads/制造中心logo.png','rb') as f: data=f.read()
> b64 = base64.b64encode(data).decode()
> open('/home/claude/logo_final_white_b64.txt','w').write(b64)
> open('/home/claude/logo_final_black_b64.txt','w').write(b64)
> ```

### 步骤 5：Playwright 截图
```python
from playwright.sync_api import sync_playwright
import os

OUT = "/home/claude/dayXX_cards"
os.makedirs(OUT, exist_ok=True)

CARDS = [
    (".card-cover",    "01_封面"),
    (".card-mgmt",     "02_管理层故事"),
    (".card-eng",      "03_工程师故事"),
    (".card-explain",  "04_概念解释"),
    (".card-scenes",   "05_实战场景"),
    (".card-mistakes", "06_误区技巧"),
    (".card-close",    "07_总结互动"),
]

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page(
        viewport={"width": 860, "height": 900},
        device_scale_factor=3   # 高清：输出 2580×2700px
    )
    page.goto("file:///home/claude/dayXX.html")
    page.wait_for_timeout(3000)  # ⚠️ 必须等待！否则 Noto Sans SC 字体未加载，中文乱码
    for sel, name in CARDS:
        el = page.query_selector(sel)
        if el:
            el.screenshot(path=f"{OUT}/DayXX_{name}.png")
    browser.close()
```

### 步骤 6：输出到交付目录
```bash
cp /home/claude/dayXX_cards/*.png /mnt/user-data/outputs/
cp /home/claude/dayXX.html /mnt/user-data/outputs/DayXX_术语名_含logo版.html
```

---

## 五、内容质量标准

### 事实准确性（最重要）

**AI 模型参数量规则：**
- ✅ 开源模型（Llama/Phi-4/Gemma）：以官方 HuggingFace/论文数据为准，可写具体数字
- ❌ 闭源模型（Claude/GPT/Gemini）：Anthropic/OpenAI/Google 均未披露，**绝对不得编造固定数值**
- 正确写法：「官方未披露」「业界估算区间」
- 参考：Phi-4 官方 14B，Phi-4-mini 官方 3.8B，Llama 3.1 官方 70B

**技术参数规则：**
- 工艺参数（温度/压力/时间）不得凭 AI 生成直接写入内容，需核查后注明来源
- AI 性能数据（误报率/准确率）应标注为「示例数据」

**版本号规则：**
- 定期核查模型版本（如 GPT-5.4、Claude Opus 4.6），以官方最新为准
- 联网搜索确认最新版本信息

### 公司/品牌匿名化规则
- ❌ 不得写「传音手机」「华为」「富士康」等具体公司名作为案例
- ✅ 必须使用匿名表述：「某头部手机制造企业」「某消费电子大厂」
- 原因：避免被误读为已验证的实际案例

### 内容审查清单
- [ ] 所有精确数字是否有来源依据？
- [ ] 闭源模型参数量是否标注「未披露」？
- [ ] 公司名称是否匿名化？
- [ ] 技术原理描述是否准确？
- [ ] 类比是否恰当（不引起误解）？
- [ ] 误区内容是否基于真实常见误解？

---

## 六、选题策略

### 封面标题公式
好标题 = 工厂场景的「反常识发现」或「数字冲击」+ 行动词 + 👇

优秀示例：
- 「设备坏了才修已经落伍了——AI提前14天知道哪台机器要出问题 👇」
- 「AOI误报率8%——每天有多少工时在无效返工？AI质检怎么把它降到0.8% 👇」
- 「同样是让AI"懂工厂"，为什么有人选微调、有人选RAG？今天讲清楚 👇」

### 类比素材库

| 技术概念 | 已用类比 |
|----------|---------|
| Fine-tuning | 读医学院（刻入大脑） |
| RAG | 执业医师+参考书 |
| 端侧AI | 厨师在餐厅现做 |
| 云侧AI | 快递到中央厨房 / 三甲医院会诊 |
| AI幻觉 | 自信的失忆者 |
| 参数规模 | 图书馆藏书量 |
| AGI | 完美的瑞士军刀 |
| 预测维护 | 给设备戴Apple Watch |
| Token | 汉字拆偏旁 |
| Context Window | 工作台大小 |

---

## 七、30 天选题配色参考

| 周次 | 主题 | 已用配色（避免重复） |
|------|------|-------------------|
| 第一周 | AI基础 | 红/蓝/绿/紫/琥珀橙/冰川青/玫瑰粉 |
| 第二周 | AI技术 | 深金铜黄/电光绿/靛蓝紫/珊瑚橙红/深邃靛青/翡翠绿/熔岩琥珀 |
| 第三周 | AI类型 | 霓虹品红/电光蓝极光青/钢铁银蓝/红外橙扫描青/深森林绿/午夜蓝电气金/深酒红铁锈橙 |
| 第四周 | AI应用 | 深空蓝冰川青/石墨黑磷光绿/深海蓝珊瑚橙/工程靛蓝荧光黄/深炭黑心跳橙脉冲绿/纯黑扫描青警示红/宇宙黑星云紫金/深墨幽灵绿警示品红/宇宙黎明日出橙极光青 |

---

## 八、常见问题排查

### Q：截图中文字体显示异常（方框或系统字体）
A：`page.wait_for_timeout(3000)` 是必须的，等待 Google Fonts 加载。若仍失败，检查网络是否能访问 fonts.googleapis.com。

### Q：Logo 注入后位置偏移
A：确认 HTML 中每张卡片有 `position:relative`，Logo div 有 `position:absolute; z-index:20`。

### Q：某张卡片截图为空
A：`page.query_selector(sel)` 返回 None 时会跳过。检查 CSS 类名是否与 CARDS 列表一致。

### Q：Logo b64 文件不存在
A：每次新会话后需检查文件是否存在。如不存在，执行步骤4注释中的重新生成代码。

### Q：如何扩展到其他行业（非制造业）
A：将「工厂场景」替换为目标行业场景，调整卡片5的实战案例，其余结构和流程保持不变。

---

## 九、交付物清单

每期生产完成后应提供：
- [ ] 7 张 PNG 截图（命名：`DayXX_术语_01_封面.png` ... `07_总结互动.png`）
- [ ] 1 个 HTML 源文件（命名：`DayXX_术语名_含logo版.html`）
- [ ] 位于 `/mnt/user-data/outputs/` 目录

可选（周期性整理）：
- [ ] 30天信息图（项目复盘用）
- [ ] 封面标题汇总文档

