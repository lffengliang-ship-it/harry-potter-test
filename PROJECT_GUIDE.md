# 霍格沃茨学院分院测试 —— 开发过程全记录

> 本文档面向没有计算机专业背景的 Web 开发者，记录了一个完整的 H5 性格测试页面从无到有的全过程。你可以把它当作一个"操作手册"，按照类似的步骤，做出属于你自己的测试页面。

---

## 一、项目概述

### 1.1 这是什么？

这是一个**哈利波特世界观下的性格测试 H5 页面**。用户回答 8 道情境选择题后，页面会告诉他：
- 属于霍格沃茨哪个学院
- 最像该学院的哪个角色
- 角色的性格特点、擅长魔法
- 角色在魔法世界经历过的名场面故事

### 1.2 为什么要做这个项目？

类似的项目（如苏东坡性格测试）在朋友圈有很高的传播性。这类项目的特点是：
- **轻量级**：不需要后端服务器，纯前端即可
- **易传播**：结果有故事性，用户愿意分享
- **低门槛**：HTML/CSS/JS 即可实现

### 1.3 最终效果

- 首屏：霍格沃茨风格标题 + 四个学院色图标 + 开始按钮
- 答题：8 道题，每题 4 个选项，带进度指示
- 结果：所属学院、最像角色、角色剧照（占位图）、性格特点标签、擅长魔法、名场面故事、四维度雷达图
- 分享：一键分享测试结果

---

## 二、技术选型（为什么选这些）

| 技术 | 选择 | 原因 |
|------|------|------|
| HTML/CSS/JS | 纯前端，无框架 | 简单、轻量、一个文件搞定全部 |
| Canvas API | 绘制雷达图 | 浏览器原生支持，无需额外库 |
| GitHub Pages | 免费托管 | 零成本部署，自动 HTTPS |
| 单文件架构 | index.html 包含全部 | 部署简单，只有一个入口 |

**为什么不选 React/Vue？**
- 这个项目没有复杂的状态管理
- 单文件更容易理解和修改
- 部署更简单（不需要构建步骤）

---

## 三、从零开始的步骤

### Step 1: 创建项目目录

```bash
mkdir harry-potter-test
cd harry-potter-test
git init
```

### Step 2: 创建 index.html 骨架

核心结构：
- `<head>`: 引入 viewport 设置、CSS 样式
- `<body>`: 三个屏幕（首屏/答题屏/结果屏），用 CSS 控制显示哪个
- `<script>`: 全部 JavaScript 逻辑

**关键设计：三屏切换**

用 CSS 的 `.screen.active` 控制显示：
```css
.screen { display: none; }
.screen.active { display: block; }
```

切换时只需要添加/移除 `active` 类：
```javascript
document.getElementById('start-screen').classList.remove('active');
document.getElementById('quiz-screen').classList.add('active');
```

### Step 3: 分离配置（config.js）

把可能变化的配置（角色图片路径、学院颜色）单独放到 `config.js`：

```javascript
export const CHARACTER_IMAGES = {
  harry: 'assets/characters/harry.jpg',
  // ... 其他角色
};

export const HOUSE_COLORS = {
  gryffindor: { primary: '#740001', secondary: '#ffd700', name: '格兰芬多' },
  // ... 其他学院
};
```

**好处**：以后换图片只需要改 config.js，不用碰业务代码。

### Step 4: 设计题目

题目用数组存储，每道题有 4 个选项，分别对应 4 个学院：

```javascript
const QUESTIONS = [
  {
    id: 1,
    question: '你在霍格沃茨的走廊里听到奇怪的声音...',
    options: [
      { text: '立刻冲过去查看', house: 'gryffindor' },
      { text: '叫上朋友一起去', house: 'hufflepuff' },
      { text: '躲在暗处分析', house: 'ravenclaw' },
      { text: '判断有没有好处', house: 'slytherin' },
    ],
  },
  // ... 共 8 题
];
```

**设计原则**：
- 情境化（代入魔法世界场景）
- 选项有趣、有画面感
- 不要直接问性格（如"你勇敢吗？"），而是通过行为推断

### Step 5: 设计角色数据库

```javascript
const CHARACTERS = {
  gryffindor: [
    {
      id: 'harry',
      name: '哈利·波特',
      title: '大难不死的男孩',
      traits: ['勇敢', '正义', '牺牲精神', '领导天赋'],
      magic: '缴械咒、守护神咒',
      story: '11 岁那年...（200字左右的故事）',
    },
    // ... 更多角色
  ],
  // ... 其他学院
};
```

**角色故事的关键**：要有传播性，有金句，有情感共鸣。

### Step 6: 实现答题逻辑

核心流程：
1. `renderQuestion()` — 渲染当前题目
2. `selectAnswer(house)` — 记录选择，加分，进入下一题
3. `getResult()` — 计算最高分学院，处理平局

**平局处理**：如果两个学院同分，按用户答题时第一次选择该学院的顺序优先。

```javascript
function getResult() {
  // 1. 找出最高分
  // 2. 如果有多个最高分，取用户最先选择的那个
  // 3. 在该学院中随机选一个角色
}
```

### Step 7: 实现结果页

结果页展示：
- 学院名称（用学院主题色）
- 角色剧照（img 标签，onerror 时显示占位图）
- 角色名称 + 头衔
- 性格特点标签
- 擅长魔法
- 名场面故事
- 雷达图

**占位图机制**：
```html
<div class="character-image">
  <img src="assets/characters/harry.jpg"
       onerror="this.style.display='none'; this.nextElementSibling.style.display='flex';">
  <div class="character-placeholder" style="display:none">
    <span>哈</span>  <!-- 角色名字首字 -->
  </div>
</div>
```

### Step 8: 绘制雷达图

用 Canvas API 绘制四边形雷达图：
- 4 个顶点：勇气/忠诚/智慧/野心
- 背景网格（4 层）
- 轴线
- 用户的分数多边形

```javascript
function drawRadarChart(scores) {
  const canvas = document.getElementById('radarCanvas');
  const ctx = canvas.getContext('2d');
  // ... 绘制网格、轴线、标签、数据
}
```

### Step 9: 响应式适配

用 CSS 媒体查询适配手机：
```css
@media (max-width: 480px) {
  h1 { font-size: 1.6rem; }
  .option-btn { padding: 14px; }
  // ... 其他缩小调整
}
```

### Step 10: 分享功能

```javascript
function shareResult() {
  if (navigator.share) {
    // 手机原生分享
    navigator.share({ title: '...', text: '...' });
  } else if (navigator.clipboard) {
    // 复制到剪贴板
    navigator.clipboard.writeText(text).then(() => alert('已复制！'));
  }
}
```

---

## 四、如何定制你自己的测试

### 4.1 修改主题

1. 改颜色：在 CSS 中修改 `background`、`color` 值
2. 改标题：在 HTML 中改 `<h1>` 和 `<title>`
3. 改题目：在 `QUESTIONS` 数组中修改

### 4.2 添加角色图片

1. 准备角色图片（建议 300x400px）
2. 放入 `assets/characters/` 目录
3. 修改 `config.js` 中的路径

### 4.3 修改角色数据

在 `CHARACTERS` 对象中修改/添加角色。注意：
- 每个学院至少要有 1 个角色
- `id` 要和 `CHARACTER_IMAGES` 中的 key 对应
- `story` 字段要有故事性和传播性

### 4.4 修改题目数量

- 在 `QUESTIONS` 数组中增删题目
- 修改 `renderQuestion()` 中的进度显示：`${currentQuestion + 1} / ${QUESTIONS.length}`

---

## 五、部署到 GitHub Pages

### 5.1 创建 GitHub 仓库

1. 打开 github.com，新建仓库 `harry-potter-test`
2. 不勾选 "Initialize this repository with a README"

### 5.2 推送代码

```bash
git remote add origin https://github.com/你的用户名/harry-potter-test.git
git branch -M main
git push -u origin main
```

### 5.3 启用 GitHub Pages

1. 打开仓库页面 → Settings → Pages
2. Source: Deploy from a branch
3. Branch: main → / (root) → Save
4. 等待 1-2 分钟

### 5.4 访问

`https://你的用户名.github.io/harry-potter-test/`

---

## 六、常见问题

**Q: 为什么打开 index.html 后页面空白？**
A: 因为使用了 `type="module"`，需要用本地服务器打开，不能直接双击文件。运行 `python3 -m http.server 8000` 后访问 `http://localhost:8000`。

**Q: 角色图片显示不出来？**
A: 检查 `config.js` 中的路径是否正确，图片是否放在了 `assets/characters/` 目录下。

**Q: 怎么修改结果页的样式？**
A: 在 `<style>` 中搜索 `.result-` 开头的 CSS 类，修改颜色、字体大小等。

**Q: 想增加更多题目怎么办？**
A: 在 `QUESTIONS` 数组中按格式添加新题目，每个选项指定一个 `house`。

**Q: 可以在微信/朋友圈分享吗？**
A: GitHub Pages 的链接可以直接分享。如果想做微信内的自定义分享卡片，需要后端支持（超出本文档范围）。

---

## 七、项目文件速查

| 文件 | 作用 |
|------|------|
| `index.html` | 主页面，包含 HTML 结构 + CSS 样式 + JS 逻辑 |
| `config.js` | 配置文件，角色图片路径 + 学院颜色 |
| `assets/characters/` | 角色图片存放目录 |
| `README.md` | 项目说明和部署指南 |

---

## 八、开发时间线

本项目通过 10 个 Task 逐步完成：

| Task | 内容 | 关键产出 |
|------|------|----------|
| 1 | 项目骨架与首屏 | index.html, README.md |
| 2 | 配置模块 | config.js |
| 3 | 题库数据 | QUESTIONS 数组 |
| 4 | 角色数据库 | CHARACTERS 对象 |
| 5 | 答题逻辑 | 计分 + 平局处理 |
| 6 | 结果页 | 角色展示 + 占位图 |
| 7 | 雷达图 | Canvas 可视化 |
| 8 | 响应式与打磨 | 移动端适配 + 分享 |
| 9 | 资源目录 | assets/characters/ |
| 10 | 部署 | GitHub Pages |

---

*本文档由 Claude Code 自动生成，作为项目开发过程的完整记录。*
