# 哈利波特学院性格测试 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 创建一个哈利波特世界观下的性格测试 H5 页面，通过 8 道情境选择题，让用户知道自己属于哪个学院、最像哪个角色，并展示角色剧照、特点、擅长魔法和名场面故事。

**Architecture:** 纯前端 HTML/CSS/JS 单页面应用。`index.html` 包含全部业务逻辑，`config.js` 管理角色图片等配置，`assets/characters/` 存放角色图片。使用 GitHub Pages 部署。

**Tech Stack:** HTML5, CSS3, Vanilla JavaScript, Canvas API (雷达图), GitHub Pages

**待确认事项处理说明：**
- 雷达图：确认实现（Task 7）
- 首屏引导：确认实现（Task 1 首屏即引导页）
- 题库随机抽题：本次版本暂不实现，固定 8 题
- 原创角色：使用书中角色，不补充原创
- 平局处理：同分情况下按答题顺序优先（第一个达到最高分的学院）

---

## File Structure

```
harry-potter-test/
├── index.html              # 主页面：UI + 全部业务逻辑
├── config.js               # 角色图片路径配置（可替换）
├── assets/
│   └── characters/         # 角色剧照文件夹
│       ├── harry.jpg       # 占位图（CSS 生成，无版权风险）
│       ├── hermione.jpg
│       ├── ron.jpg
│       ├── newt.jpg
│       ├── cedric.jpg
│       ├── luna.jpg
│       ├── cho.jpg
│       ├── snape.jpg
│       └── draco.jpg
└── README.md               # 项目说明 + 部署指南
```

**文件职责：**
- `index.html`：唯一入口，包含所有 HTML 结构、CSS 样式、JS 逻辑
- `config.js`：导出角色图片路径映射对象，业务代码 `import` 使用
- `assets/characters/`：存放角色图片，默认用 CSS 占位图，用户可替换
- `README.md`：项目介绍、使用说明、部署步骤

---

## Task 1: 项目骨架与首屏页面

**Files:**
- Create: `index.html`
- Create: `README.md`

- [ ] **Step 1: 创建项目基础 HTML 结构**

在 `index.html` 中写入：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
  <title>霍格沃茨学院分院测试</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: 'Georgia', 'Noto Serif SC', serif;
      background: #1a1a2e;
      color: #e8e8e8;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 20px;
    }
    .container {
      max-width: 480px;
      width: 100%;
      text-align: center;
    }
    .screen { display: none; }
    .screen.active { display: block; animation: fadeIn 0.6s ease; }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(20px); }
      to { opacity: 1; transform: translateY(0); }
    }
    h1 { font-size: 2rem; margin-bottom: 16px; color: #ffd700; }
    .subtitle { font-size: 1rem; color: #b0b0b0; margin-bottom: 32px; line-height: 1.6; }
    .btn {
      display: inline-block;
      padding: 14px 40px;
      background: #740001;
      color: #fff;
      border: none;
      border-radius: 8px;
      font-size: 1.1rem;
      cursor: pointer;
      transition: all 0.3s ease;
      font-family: inherit;
    }
    .btn:hover { background: #9a0001; transform: scale(1.02); }
    .houses { display: flex; justify-content: center; gap: 12px; margin: 24px 0; }
    .house-icon {
      width: 48px; height: 48px; border-radius: 50%;
      display: flex; align-items: center; justify-content: center;
      font-size: 1.5rem; font-weight: bold;
    }
    .gryffindor { background: #740001; color: #ffd700; }
    .hufflepuff { background: #ecb939; color: #372e29; }
    .ravenclaw { background: #0e1a40; color: #946b2d; }
    .slytherin { background: #1a472a; color: #aaaaaa; }
  </style>
</head>
<body>
  <div class="container">
    <!-- 首屏 -->
    <div id="start-screen" class="screen active">
      <h1>霍格沃茨分院测试</h1>
      <p class="subtitle">穿越九又四分之三站台，<br>测测你属于哪个学院，最像哪位巫师</p>
      <div class="houses">
        <div class="house-icon gryffindor">G</div>
        <div class="house-icon hufflepuff">H</div>
        <div class="house-icon ravenclaw">R</div>
        <div class="house-icon slytherin">S</div>
      </div>
      <button class="btn" onclick="startQuiz()">开始测试</button>
    </div>

    <!-- 题目屏 -->
    <div id="quiz-screen" class="screen"></div>

    <!-- 结果屏 -->
    <div id="result-screen" class="screen"></div>
  </div>

  <script type="module">
    // 导入配置模块（后续任务会用到）
    import { HOUSE_COLORS } from './config.js';

    function startQuiz() {
      document.getElementById('start-screen').classList.remove('active');
      document.getElementById('quiz-screen').classList.add('active');
    }
    window.startQuiz = startQuiz;
  </script>
</body>
</html>
```

- [ ] **Step 2: 验证首屏显示**

打开 `index.html`（直接在浏览器中打开或启动本地服务器）。

Expected: 看到深色背景、金色标题 "霍格沃茨分院测试"、四个学院色圆形图标、红色"开始测试"按钮。点击按钮能切换到空白题目屏。

- [ ] **Step 3: 创建 README.md**

```markdown
# 霍格沃茨学院分院测试

哈利波特世界观下的性格测试 H5 页面。

## 功能
- 8 道情境选择题
- 四大学院分院
- 角色匹配 + 剧照展示
- 角色特点、擅长魔法、名场面故事

## 本地预览
```bash
# 方式1：直接打开
open index.html

# 方式2：本地服务器
python3 -m http.server 8000
# 访问 http://localhost:8000
```

## 部署到 GitHub Pages
1. 创建 GitHub 仓库 `harry-potter-test`
2. 推送代码到 `main` 分支
3. Settings → Pages → Source: Deploy from a branch → main → / (root)
4. 访问 `https://<你的用户名>.github.io/harry-potter-test/`

## 替换角色图片
1. 准备角色图片，放入 `assets/characters/`
2. 修改 `config.js` 中的图片路径
3. 无需修改业务代码
```

- [ ] **Step 4: Commit**

```bash
git add index.html README.md
git commit -m "feat: project skeleton and start screen"
```

---

## Task 2: 配置模块

**Files:**
- Create: `config.js`

- [ ] **Step 1: 创建 config.js**

```javascript
// config.js - 角色图片配置
// 默认使用占位图路径，用户可替换为真实图片

export const CHARACTER_IMAGES = {
  harry: 'assets/characters/harry.jpg',
  hermione: 'assets/characters/hermione.jpg',
  ron: 'assets/characters/ron.jpg',
  newt: 'assets/characters/newt.jpg',
  cedric: 'assets/characters/cedric.jpg',
  luna: 'assets/characters/luna.jpg',
  cho: 'assets/characters/cho.jpg',
  snape: 'assets/characters/snape.jpg',
  draco: 'assets/characters/draco.jpg',
};

// 学院颜色配置
export const HOUSE_COLORS = {
  gryffindor: { primary: '#740001', secondary: '#ffd700', name: '格兰芬多' },
  hufflepuff: { primary: '#ecb939', secondary: '#372e29', name: '赫奇帕奇' },
  ravenclaw: { primary: '#0e1a40', secondary: '#946b2d', name: '拉文克劳' },
  slytherin: { primary: '#1a472a', secondary: '#aaaaaa', name: '斯莱特林' },
};
```

- [ ] **Step 2: 验证模块导出**

在 `index.html` 的 `<script>` 中临时添加：

```javascript
import { CHARACTER_IMAGES, HOUSE_COLORS } from './config.js';
console.log(CHARACTER_IMAGES);
console.log(HOUSE_COLORS);
```

打开浏览器控制台，Expected: 看到两个对象正常输出，无报错。

- [ ] **Step 3: Commit**

```bash
git add config.js
git commit -m "feat: add config module for character images and house colors"
```

---

## Task 3: 题库数据

**Files:**
- Modify: `index.html`（在 `<script>` 中添加数据）

- [ ] **Step 1: 定义题目数据**

在 `index.html` 的 `<script type="module">` 中添加：

```javascript
// 题库：8 道情境选择题
// 每题 4 个选项，分别对应 G/H/R/S 四个学院
const QUESTIONS = [
  {
    id: 1,
    question: '你在霍格沃茨的走廊里听到奇怪的声音，像是有人在哭泣。你会：',
    options: [
      { text: '立刻冲过去查看，哪怕可能有危险', house: 'gryffindor' },
      { text: '先去叫上朋友，一起去看看', house: 'hufflepuff' },
      { text: '躲在暗处观察，分析声音来源和原因', house: 'ravenclaw' },
      { text: '判断这件事对自己有没有好处，再决定', house: 'slytherin' },
    ],
  },
  {
    id: 2,
    question: '魔药课上，你的坩埚突然爆炸，斯内普教授冷冷看着你。你会：',
    options: [
      { text: '承认错误，但内心不服输，下次一定做好', house: 'gryffindor' },
      { text: '诚恳道歉，主动清理，并向同学求助', house: 'hufflepuff' },
      { text: '立刻回忆配方步骤，找出爆炸的化学原理', house: 'ravenclaw' },
      { text: '观察教授表情，判断他是真生气还是在试探', house: 'slytherin' },
    ],
  },
  {
    id: 3,
    question: '你发现了一个可以进入禁林的秘密通道，你会：',
    options: [
      { text: '今晚就去探险！危险才刺激', house: 'gryffindor' },
      { text: '先告诉信任的朋友，征得同意一起去', house: 'hufflepuff' },
      { text: '研究地图和书籍，制定详细计划再去', house: 'ravenclaw' },
      { text: '评估里面的资源和风险，准备充分后独自前往', house: 'slytherin' },
    ],
  },
  {
    id: 4,
    question: '魁地奇选拔赛上，你的朋友和对手发生激烈冲突，你会：',
    options: [
      { text: '直接站出来替朋友出头，不管对手多强', house: 'gryffindor' },
      { text: '调解冲突，努力让双方都冷静下来', house: 'hufflepuff' },
      { text: '分析规则，找出对己方最有利的解决方案', house: 'ravenclaw' },
      { text: '利用冲突制造机会，让自己获得上场资格', house: 'slytherin' },
    ],
  },
  {
    id: 5,
    question: '期末考试前一周，你最可能：',
    options: [
      { text: '临时抱佛脚，但相信直觉和勇气能过关', house: 'gryffindor' },
      { text: '按部就班复习，每天固定时间学习', house: 'hufflepuff' },
      { text: '整理知识体系，制作思维导图和笔记', house: 'ravenclaw' },
      { text: '找到往年考题，分析出题规律，针对性准备', house: 'slytherin' },
    ],
  },
  {
    id: 6,
    question: '有人散布关于你的恶意谣言，全校都在议论。你会：',
    options: [
      { text: '当众 confront 造谣者，正面硬刚', house: 'gryffindor' },
      { text: '找信任的人倾诉，用行动证明自己', house: 'hufflepuff' },
      { text: '调查谣言源头，收集证据理性反击', house: 'ravenclaw' },
      { text: '观察谁信了谣言，谁是可利用的盟友', house: 'slytherin' },
    ],
  },
  {
    id: 7,
    question: '你获得了一个强大的魔法物品，但使用它可能有副作用。你会：',
    options: [
      { text: '为了正义的目的使用它，承担后果', house: 'gryffindor' },
      { text: '只在必要时使用，尽量保护自己和他人', house: 'hufflepuff' },
      { text: '深入研究它的原理，找到安全使用的方法', house: 'ravenclaw' },
      { text: '权衡利弊，在最关键的时刻用它达成目标', house: 'slytherin' },
    ],
  },
  {
    id: 8,
    question: '如果只能带一样东西进阿兹卡班，你会选：',
    options: [
      { text: '格兰芬多宝剑——即使在绝望中也要反抗', house: 'gryffindor' },
      { text: '家人的照片——爱和回忆是最后的温暖', house: 'hufflepuff' },
      { text: '一本魔法书——知识是越狱的唯一希望', house: 'ravenclaw' },
      { text: '复方汤剂配方——换个身份就能重获自由', house: 'slytherin' },
    ],
  },
];
```

- [ ] **Step 2: 验证数据完整性**

在控制台执行：

```javascript
console.log(QUESTIONS.length); // Expected: 8
console.log(QUESTIONS[0].options.length); // Expected: 4
console.log(QUESTIONS.every(q => q.options.every(o => ['gryffindor','hufflepuff','ravenclaw','slytherin'].includes(o.house)))); // Expected: true
```

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add 8 quiz questions with house mapping"
```

---

## Task 4: 角色数据库

**Files:**
- Modify: `index.html`（在 `<script>` 中添加数据）

- [ ] **Step 1: 定义角色数据**

在 `index.html` 的 `<script>` 中继续添加：

```javascript
// 角色数据库
const CHARACTERS = {
  gryffindor: [
    {
      id: 'harry',
      name: '哈利·波特',
      title: '大难不死的男孩',
      traits: ['勇敢', '正义', '牺牲精神', '领导天赋'],
      magic: '缴械咒 (Expelliarmus)、守护神咒 (Expecto Patronum)',
      story: '11 岁那年，哈利从德思礼家的碗柜里收到了霍格沃茨的录取通知书。在禁林里，他面对伏地魔的奇洛教授，用母亲留下的爱保护了魔法石。三强争霸赛中，他和塞德里克一起举起了火焰杯，却一起被传送到伏地魔复活的墓地。他眼睁睁看着塞德里克被杀死，却带着他的遗体回到了霍格沃茨。最终，他在霍格沃茨大战中假死重生，用老魔杖击败了伏地魔——这个他命中注定的敌人。',
    },
    {
      id: 'hermione',
      name: '赫敏·格兰杰',
      title: '同龄中最聪明的巫师',
      traits: ['智慧', '勇敢', '忠诚', '正义感强'],
      magic: '除你武器、时间转换器操控、高级魔咒',
      story: '一年级时，她就已经能记住所有课本内容，却因"爱出风头"被同学排斥。直到万圣节那天，她和哈利、罗恩一起对抗山怪，三人的友谊从此开始。她用一个时间转换器同时上多门课，还用「一忘皆空」修改了父母的记忆以保护他们。在寻找魂器的旅途中，她用无痕伸展咒带着一个帐篷和无数书籍，是三人组不可或缺的智囊。',
    },
    {
      id: 'ron',
      name: '罗恩·韦斯莱',
      title: '最忠诚的朋友',
      traits: ['幽默', '忠诚', '勇敢', '愿为朋友牺牲'],
      magic: '巫师棋战术、魁地奇守门',
      story: '一年级时，他在巨大的巫师棋盘上为了朋友和学院的荣誉，甘愿牺牲自己——「我来做一个骑士」。从那以后，他无数次站在哈利身边：驾驶福特安格里亚车追火车、在禁林里面对蜘蛛的恐惧、离开家庭去寻找魂器。他曾因嫉妒暂时离开，但归来时从冰冷的湖水中救了哈利，并用格兰芬多宝剑摧毁了魂器挂坠盒。',
    },
  ],
  hufflepuff: [
    {
      id: 'newt',
      name: '纽特·斯卡曼德',
      title: '神奇动物学家',
      traits: ['善良', '执着', '热爱自然', '温柔坚韧'],
      magic: '神奇动物沟通、无痕伸展咒',
      story: '纽特带着一个装满神奇动物的皮箱环游世界。他的皮箱里有一片完整的生态系统：雷鸟在云端翱翔，嗅嗅偷走所有闪亮的东西，护树罗锅帮他开锁。他被霍格沃茨开除过，但邓布利多为他辩护。当格林德沃崛起时，他本可以置身事外，却选择了站出来——不是因为野心，而是因为「那些动物需要保护，而保护它们就是正确的事」。',
    },
    {
      id: 'cedric',
      name: '塞德里克·迪戈里',
      title: '真正的勇士',
      traits: ['公平', '诚实', '勇敢', '善良'],
      magic: '变形咒、魁地奇搜捕手',
      story: '他是赫奇帕奇最出色的学生，三强争霸赛的勇士之一。在第一个项目中，他公平地告诉了哈利龙蛋的秘密；在第三个项目中，迷宫里他和哈利同时找到了火焰杯，他坚持「我们一起拿，这是公平的」。这个决定让他被传送到伏地魔复活的墓地，死在了「阿瓦达索命」之下——年仅 17 岁。哈利带着他的遗体回到霍格沃茨，整个霍格沃茨都在哭泣。',
    },
  ],
  ravenclaw: [
    {
      id: 'luna',
      name: '卢娜·洛夫古德',
      title: '疯姑娘',
      traits: ['独特思考', '智慧', '真诚', '富有想象力'],
      magic: '召唤守护灵、看到夜骐',
      story: '她从小就能看到夜骐——只有见过死亡的人才能看见。她的母亲在做实验时意外去世，父亲经营着《唱唱反调》杂志。她被同学嘲笑为"疯姑娘"，却在霍格沃茨大战中展现了非凡的勇气：她用「除你武器」击昏了食死徒，还帮助哈利找到了拉文克劳的冠冕——魂器之一。战后，她成为了著名的自然学家，发现和分类了许多新的神奇动物。',
    },
    {
      id: 'cho',
      name: '秋·张',
      title: '拉文克劳找球手',
      traits: ['优雅', '感性', '聪慧', '重情重义'],
      magic: '高级魔咒、魁地奇飞行',
      story: '她是拉文克劳魁地奇球队的找球手，也是哈利·波特的初恋。她在塞德里克死后加入了邓布利多军，学习防御术来对抗食死徒。在霍格莫德村的训练课上，她因玛丽埃塔的背叛而暴露了 D.A. 的秘密据点，从此背负着愧疚。大战中，她回到了霍格沃茨，与曾经的同学们并肩作战——这一次，她不再只是塞德里克的女朋友，而是选择了自己站队的战士。',
    },
  ],
  slytherin: [
    {
      id: 'snape',
      name: '西弗勒斯·斯内普',
      title: '最勇敢的人',
      traits: ['复杂', '牺牲', '深情', '双重身份'],
      magic: '魔药大师、大脑封闭术、黑魔法防御',
      story: '他的一生都在扮演双面间谍。表面上他是伏地魔最信任的食死徒，实际上他从莉莉·波特死后就一直在保护哈利。他冒着生命危险给邓布利多传递情报，在伏地魔身边隐藏了多年。最后，他在尖叫棚屋被纳吉尼咬死，临死前把记忆给了哈利——那些记忆揭示了他对莉莉一生的爱，以及他真正的忠诚。「看着我」——他最后的话，是想在死前再看一次和莉莉一样的绿色眼睛。',
    },
    {
      id: 'draco',
      name: '德拉克·马尔福',
      title: '被宠坏的少爷',
      traits: ['精致利己', '摇摆不定', '家族荣誉', '内心挣扎'],
      magic: '大脑封闭术、决斗咒',
      story: '他从小被灌输纯血统的优越感，却在六年级时接到了一个不可能完成的任务：杀死邓布利多。他花了整个学期修理消失柜，让食死徒潜入霍格沃茨。但在天文塔上，当邓布利多毫无防备地站在他面前时，他下不了手。他降下了邓布利多的魔杖，却没有念出杀戮咒。在霍格沃茨大战中，他和父母一起逃离了战场——他从来不是真正的恶人，只是一个被家族裹挟的少年。',
    },
  ],
};
```

- [ ] **Step 2: 验证角色数据**

```javascript
console.log(Object.keys(CHARACTERS)); // Expected: ['gryffindor', 'hufflepuff', 'ravenclaw', 'slytherin']
console.log(CHARACTERS.gryffindor.length); // Expected: 3
console.log(CHARACTERS.slytherin[0].story.length > 100); // Expected: true
```

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add character database with stories and magic"
```

---

## Task 5: 答题逻辑与计分

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 实现答题状态管理**

在 `<script>` 中添加：

```javascript
// 答题状态
let currentQuestion = 0;
let scores = { gryffindor: 0, hufflepuff: 0, ravenclaw: 0, slytherin: 0 };
let answers = [];

function renderQuestion() {
  const q = QUESTIONS[currentQuestion];
  const container = document.getElementById('quiz-screen');
  container.innerHTML = `
    <div class="progress">${currentQuestion + 1} / ${QUESTIONS.length}</div>
    <h2 class="question">${q.question}</h2>
    <div class="options">
      ${q.options.map((opt, idx) => `
        <button class="option-btn" onclick="selectAnswer('${opt.house}', ${idx})">
          <span class="option-label">${String.fromCharCode(65 + idx)}</span>
          <span class="option-text">${opt.text}</span>
        </button>
      `).join('')}
    </div>
  `;
}

function selectAnswer(house, optionIndex) {
  scores[house]++;
  answers.push({ question: currentQuestion, house, optionIndex });
  currentQuestion++;

  if (currentQuestion < QUESTIONS.length) {
    renderQuestion();
  } else {
    showResult();
  }
}

function getResult() {
  // 找出最高分学院（平局时按用户答题记录中第一个出现的最高分学院获胜）
  const entries = Object.entries(scores);
  entries.sort((a, b) => b[1] - a[1]);
  const maxScore = entries[0][1];

  // 找到所有最高分的学院
  const topHouses = entries.filter(e => e[1] === maxScore).map(e => e[0]);

  // 平局处理：按用户答题时第一次选择该学院的顺序优先
  let topHouse = topHouses[0];
  if (topHouses.length > 1) {
    // 遍历用户的答题记录，找到第一个属于topHouses的学院
    for (const answer of answers) {
      if (topHouses.includes(answer.house)) {
        topHouse = answer.house;
        break;
      }
    }
  }

  // 在该学院中随机选一个角色
  const houseCharacters = CHARACTERS[topHouse];
  const character = houseCharacters[Math.floor(Math.random() * houseCharacters.length)];

  return { house: topHouse, character, scores };
}

window.selectAnswer = selectAnswer;
```

- [ ] **Step 2: 添加题目屏样式**

在 `<style>` 中添加：

```css
.progress { color: #ffd700; font-size: 0.9rem; margin-bottom: 16px; }
.question { font-size: 1.2rem; line-height: 1.6; margin-bottom: 24px; }
.options { display: flex; flex-direction: column; gap: 12px; }
.option-btn {
  display: flex; align-items: center; gap: 12px;
  padding: 16px; background: rgba(255,255,255,0.05);
  border: 1px solid rgba(255,255,255,0.1);
  border-radius: 12px; color: #e8e8e8;
  cursor: pointer; transition: all 0.3s ease;
  text-align: left; font-family: inherit; font-size: 1rem;
}
.option-btn:hover { background: rgba(255,255,255,0.1); border-color: #ffd700; }
.option-label {
  width: 32px; height: 32px; border-radius: 50%;
  background: rgba(255,215,0,0.2); color: #ffd700;
  display: flex; align-items: center; justify-content: center;
  font-weight: bold; flex-shrink: 0;
}
```

- [ ] **Step 3: 修改 startQuiz 调用 renderQuestion**

```javascript
function startQuiz() {
  document.getElementById('start-screen').classList.remove('active');
  document.getElementById('quiz-screen').classList.add('active');
  renderQuestion();
}
```

- [ ] **Step 4: 测试答题流程**

打开页面，点击「开始测试」，Expected:
1. 显示第 1 题，进度「1 / 8」
2. 点击选项后自动进入第 2 题
3. 依次答完 8 题后，调用 `showResult()`（目前为空）
4. 控制台无报错

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: implement quiz logic and scoring"
```

---

## Task 6: 结果页与角色展示

**Files:**
- Modify: `index.html`
- Modify: `config.js`

- [ ] **Step 1: 在 config.js 末尾添加 getCharacterImage 函数**

```javascript
// 在 config.js 末尾添加：

// 获取角色图片路径
export function getCharacterImage(characterId) {
  return CHARACTER_IMAGES[characterId] || '';
}
```

- [ ] **Step 2: 实现结果页渲染**

在 `index.html` 的 `<script>` 中添加（在 Task 1 已有的 `import { HOUSE_COLORS }` 基础上，追加导入 `getCharacterImage`）：

```javascript
import { HOUSE_COLORS, getCharacterImage } from './config.js';

function showResult() {
  const result = getResult();
  const houseConfig = HOUSE_COLORS[result.house];
  const char = result.character;

  const container = document.getElementById('result-screen');
  container.innerHTML = `
    <div class="result-house" style="color: ${houseConfig.secondary}">
      ${houseConfig.name}
    </div>
    <div class="result-character">
      <div class="character-image">
        <img src="${getCharacterImage(char.id)}"
             alt="${char.name}"
             onerror="this.style.display='none'; this.nextElementSibling.style.display='flex';">
        <div class="character-placeholder" style="display:none">
          <span>${char.name[0]}</span>
        </div>
      </div>
      <h2 class="character-name">${char.name}</h2>
      <p class="character-title">${char.title}</p>
    </div>
    <div class="result-details">
      <div class="detail-section">
        <h3>性格特点</h3>
        <div class="traits">
          ${char.traits.map(t => `<span class="trait-tag">${t}</span>`).join('')}
        </div>
      </div>
      <div class="detail-section">
        <h3>擅长魔法</h3>
        <p class="magic-text">${char.magic}</p>
      </div>
      <div class="detail-section story-section">
        <h3>魔法世界名场面</h3>
        <p class="story-text">${char.story}</p>
      </div>
    </div>
    <button class="btn" onclick="restartQuiz()">再测一次</button>
  `;

  document.getElementById('quiz-screen').classList.remove('active');
  document.getElementById('result-screen').classList.add('active');
}

function restartQuiz() {
  currentQuestion = 0;
  scores = { gryffindor: 0, hufflepuff: 0, ravenclaw: 0, slytherin: 0 };
  answers = [];
  document.getElementById('result-screen').classList.remove('active');
  document.getElementById('start-screen').classList.add('active');
}

window.restartQuiz = restartQuiz;
```

- [ ] **Step 3: 添加结果页样式**

在 `<style>` 中添加：

```css
.result-house {
  font-size: 1.5rem; font-weight: bold;
  margin-bottom: 16px; letter-spacing: 4px;
}
.character-image {
  width: 200px; height: 250px; margin: 0 auto 16px;
  border-radius: 12px; overflow: hidden;
  background: rgba(255,255,255,0.05);
  display: flex; align-items: center; justify-content: center;
}
.character-image img {
  width: 100%; height: 100%; object-fit: cover;
}
.character-placeholder {
  width: 100%; height: 100%;
  display: flex; align-items: center; justify-content: center;
  background: linear-gradient(135deg, rgba(255,215,0,0.1), rgba(255,215,0,0.05));
  font-size: 4rem; color: #ffd700;
}
.character-name { font-size: 1.8rem; color: #ffd700; margin-bottom: 4px; }
.character-title { font-size: 0.95rem; color: #b0b0b0; margin-bottom: 24px; }
.result-details { text-align: left; margin: 24px 0; }
.detail-section { margin-bottom: 20px; }
.detail-section h3 {
  font-size: 1rem; color: #ffd700;
  margin-bottom: 8px; border-left: 3px solid #ffd700;
  padding-left: 10px;
}
.traits { display: flex; flex-wrap: wrap; gap: 8px; }
.trait-tag {
  padding: 4px 12px; background: rgba(255,215,0,0.1);
  border: 1px solid rgba(255,215,0,0.3);
  border-radius: 16px; font-size: 0.85rem; color: #ffd700;
}
.magic-text { font-size: 0.95rem; line-height: 1.6; color: #d0d0d0; }
.story-text {
  font-size: 0.95rem; line-height: 1.8; color: #c0c0c0;
  text-indent: 2em;
}
```

- [ ] **Step 4: 测试结果页**

答完 8 题后，Expected:
1. 显示所属学院名称（如「格兰芬多」）
2. 显示角色名称和头衔
3. 图片区域显示占位图（CSS 渐变色背景 + 角色名字首字母）
4. 显示性格特点标签
5. 显示擅长魔法
6. 显示名场面故事
7. 点击「再测一次」回到首页，控制台验证 `currentQuestion === 0` 且 `scores` 所有值为 0

- [ ] **Step 5: Commit**

```bash
git add index.html config.js
git commit -m "feat: implement result page with character display"
```

---

## Task 7: 雷达图可视化（四维度评分图）

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 添加 Canvas 雷达图**

在 `showResult()` 函数中，找到 `<div class="result-details">` 这一行，在其**前面**插入雷达图：

```javascript
// 在 showResult() 中，将以下代码插入到 "<div class=\"result-details\">" 之前：
`
  <div class="radar-chart">
    <canvas id="radarCanvas" width="300" height="300"></canvas>
  </div>
  <div class="result-details">
`

在 `<script>` 中添加绘制函数：

```javascript
function drawRadarChart(scores) {
  const canvas = document.getElementById('radarCanvas');
  const ctx = canvas.getContext('2d');
  const centerX = canvas.width / 2;
  const centerY = canvas.height / 2;
  const radius = 100;

  const houses = ['gryffindor', 'hufflepuff', 'ravenclaw', 'slytherin'];
  const labels = ['勇气', '忠诚', '智慧', '野心'];
  const maxScore = Math.max(...Object.values(scores), 1);

  // 清空画布
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // 绘制背景网格
  for (let i = 1; i <= 4; i++) {
    ctx.beginPath();
    ctx.strokeStyle = 'rgba(255, 215, 0, 0.15)';
    ctx.lineWidth = 1;
    for (let j = 0; j < 4; j++) {
      const angle = (Math.PI * 2 * j) / 4 - Math.PI / 2;
      const r = (radius * i) / 4;
      const x = centerX + r * Math.cos(angle);
      const y = centerY + r * Math.sin(angle);
      if (j === 0) ctx.moveTo(x, y);
      else ctx.lineTo(x, y);
    }
    ctx.closePath();
    ctx.stroke();
  }

  // 绘制轴线
  for (let i = 0; i < 4; i++) {
    const angle = (Math.PI * 2 * i) / 4 - Math.PI / 2;
    ctx.beginPath();
    ctx.strokeStyle = 'rgba(255, 215, 0, 0.2)';
    ctx.moveTo(centerX, centerY);
    ctx.lineTo(centerX + radius * Math.cos(angle), centerY + radius * Math.sin(angle));
    ctx.stroke();

    // 标签
    const labelX = centerX + (radius + 20) * Math.cos(angle);
    const labelY = centerY + (radius + 20) * Math.sin(angle);
    ctx.fillStyle = '#ffd700';
    ctx.font = '14px Georgia';
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText(labels[i], labelX, labelY);
  }

  // 绘制数据
  ctx.beginPath();
  ctx.fillStyle = 'rgba(255, 215, 0, 0.25)';
  ctx.strokeStyle = '#ffd700';
  ctx.lineWidth = 2;

  for (let i = 0; i < 4; i++) {
    const angle = (Math.PI * 2 * i) / 4 - Math.PI / 2;
    const value = scores[houses[i]] || 0;
    const r = (value / maxScore) * radius;
    const x = centerX + r * Math.cos(angle);
    const y = centerY + r * Math.sin(angle);
    if (i === 0) ctx.moveTo(x, y);
    else ctx.lineTo(x, y);
  }
  ctx.closePath();
  ctx.fill();
  ctx.stroke();
}
```

在 `showResult()` 最后调用：

```javascript
setTimeout(() => drawRadarChart(result.scores), 100);
```

- [ ] **Step 2: 添加雷达图样式**

```css
.radar-chart { margin: 20px 0; }
.radar-chart canvas { max-width: 100%; }
```

- [ ] **Step 3: 测试雷达图**

答完题目后，Expected: 看到一个四边形雷达图，四个顶点分别是「勇气」「忠诚」「智慧」「野心」，用户的答题结果形成一个多边形覆盖在上面。

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add radar chart for house scores"
```

---

## Task 8: 响应式与细节打磨

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 添加移动端适配**

在 `<style>` 中添加：

```css
@media (max-width: 480px) {
  h1 { font-size: 1.6rem; }
  .question { font-size: 1.05rem; }
  .option-btn { padding: 14px; }
  .character-image { width: 160px; height: 200px; }
  .character-name { font-size: 1.5rem; }
  .houses { gap: 8px; }
  .house-icon { width: 40px; height: 40px; font-size: 1.2rem; }
}
```

- [ ] **Step 2: 添加页面切换动画增强**

```css
.screen { display: none; opacity: 0; }
.screen.active { display: block; animation: fadeIn 0.5s ease forwards; }
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}
```

- [ ] **Step 3: 添加分享功能（可选）**

```javascript
function shareResult() {
  const result = getResult();
  const text = `我在霍格沃茨分院测试中属于${HOUSE_COLORS[result.house].name}，最像${result.character.name}！`;
  const url = 'https://你的用户名.github.io/harry-potter-test/';

  if (navigator.share) {
    navigator.share({ title: '霍格沃茨分院测试', text: text + ' ' + url }).catch(() => {});
  } else if (navigator.clipboard && navigator.clipboard.writeText) {
    navigator.clipboard.writeText(text + ' ' + url)
      .then(() => alert('结果已复制到剪贴板！'))
      .catch(() => alert('复制失败，请手动复制'));
  } else {
    alert('您的浏览器不支持分享功能');
  }
}
window.shareResult = shareResult;
```

在结果页「再测一次」按钮前加一个分享按钮：

```html
<button class="btn" style="background: #0e1a40; margin-right: 10px;" onclick="shareResult()">分享结果</button>
```

- [ ] **Step 4: 完整测试一遍**

从头到尾测试：
1. 首屏显示正常
2. 答题流畅，8 题都能正常跳转
3. 结果页展示完整（学院、角色、图片占位符、特点、魔法、故事、雷达图）
4. 「再测一次」能重置
5. 分享按钮可用（或 fallback 到复制）
6. 手机端显示正常

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: responsive design and polish"
```

---

## Task 9: 创建 assets 目录与占位图

**Files:**
- Create: `assets/characters/.gitkeep`

- [ ] **Step 1: 创建 assets 目录**

```bash
mkdir -p assets/characters
```

- [ ] **Step 2: 添加 .gitkeep 和 .gitignore**

```bash
touch assets/characters/.gitkeep
echo -e "*.jpg\n*.png\n*.jpeg" > assets/characters/.gitignore
```

说明：`.gitkeep` 保证空目录能被 git 追踪；`.gitignore` 避免提交真实图片（版权风险）。

- [ ] **Step 3: Commit**

```bash
git add assets/
git commit -m "chore: add assets directory with gitkeep and gitignore"
```

---

## Task 10: GitHub Pages 部署

**Files:**
- Modify: `README.md`

- [ ] **Step 1: 确保代码已推送到 GitHub**

```bash
git remote add origin https://github.com/你的用户名/harry-potter-test.git
git branch -M main
git push -u origin main
```

- [ ] **Step 2: 启用 GitHub Pages**

1. 打开 GitHub 仓库页面
2. Settings → Pages → Source: Deploy from a branch
3. Branch: main → / (root) → Save
4. 等待 1-2 分钟

- [ ] **Step 3: 验证部署**

访问 `https://<你的用户名>.github.io/harry-potter-test/`

Expected: 页面正常显示，答题流程完整，结果页正常。

---

## Summary

| Task | 内容 | 产出 |
|------|------|------|
| 1 | 项目骨架与首屏 | `index.html`, `README.md` |
| 2 | 配置模块 | `config.js` |
| 3 | 题库数据 | `index.html` 中的 QUESTIONS |
| 4 | 角色数据库 | `index.html` 中的 CHARACTERS |
| 5 | 答题逻辑与计分 | 答题流程、计分算法 |
| 6 | 结果页与角色展示 | 角色展示、图片配置化 |
| 7 | 雷达图可视化 | Canvas 四维度雷达图 |
| 8 | 响应式与打磨 | 移动端适配、动画、分享 |
| 9 | 资源目录 | `assets/characters/` |
| 10 | GitHub Pages 部署 | 线上可访问 |
