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
