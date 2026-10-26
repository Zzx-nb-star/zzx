---
name: "auto-add-links"
description: "Scans project .html files and auto-appends missing link-card entries into index.html. Invoke when user asks to 'add new pages to index', 'update homepage navigation', or after creating new .html practice pages."
---

# 自动添加导航链接 Skill

扫描项目目录下所有 `.html` 文件，对比 `index.html` 中已有的 `<a href="...".link-card>` 卡片，把缺失的自动补上。

## 触发条件

- 用户说「把新页面加到首页」「更新导航」「把所有 html 页面的超链接都加到首页」
- 刚创建了新的 HTML 练习页（如 `15-2.html`）需要加入 `index.html`
- 用户明确要求补齐首页导航

## 操作流程

### 1. 扫描 .html 文件
```
Glob *.html 在项目根目录
```
排除 `index.html` 本身。按文件名排序，便于在首页保持顺序。

### 2. 读取 index.html
用正则 / 字符串解析已有的 `href="xxx.html"` 集合：
```
/href="([^"]+\.html)"/gi
```

### 3. 差集 = 需要新增的文件
对每个缺失文件，生成一条 `link-card` HTML：
```html
<a class="link-card" href="文件名.html" target="_blank">
  <span class="tag">页面 N</span>
  <h3>文件名.html</h3>
  <p>一句话描述（根据文件名/页面内容推断）。</p>
</a>
```

### 4. 插入位置
找到 index.html 中最后一个 `</a>` + `</section>`（即 `links` 容器末尾闭合标签前），把新卡片插进去。

### 5. 编号规则
沿用现有「页面 N」递增方式，按旧卡片最大编号 +1 开始。

## 注意事项

- **只增不改**：不改动已有卡片的位置、样式、描述
- **去重**：严格以文件名为 key，已存在的一律跳过
- **空格/中文文件名**：`href` 里保留原样，不要 URL encode（现代浏览器都支持）
- **CSS 复用**：直接用已有的 `.link-card` / `.tag` 样式类，不新写 CSS
