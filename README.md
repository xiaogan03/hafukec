# 信息予你无限 · 管理后台（静态复刻版）

原站：`https://df.qq.com/cp/havvkrelink/admin/`（三角洲行动活动页）

## 文件结构

```
哈弗克/
├── admin-static.html   ← 唯一运行文件（双击打开）
├── 字体/               ← 本地字体（方正兰亭黑系列）
│   ├── fzlth.woff
│   ├── fzltzh.woff
│   └── fzltth.woff
└── README.md
```

## 账号密码

| 用途 | 值 |
|------|-----|
| 登录 | 用户名 `111` / 密码 `111` |
| 文件夹加密（人员伤亡事故调查） | `123` |
| 文档加密（事故调查报告） | `123` |

## 核心技术

- 单文件 HTML，CSS/JS 全部内联
- rem 自适应，基准 `calc(100 / 3840 * 100vw)`（3840 设计稿）
- 纯原生 JS，无 jQuery 依赖
- 所有模拟数据在 JS 的 `mockData` 对象中

## 页面结构（4 层）

```
登录页 → 目录页 → 文档列表页 → 文档详情页
                    ↘ 图片查看器
         ↘ 密码弹窗（加密项目）
```

## 图标规则

| 文档类型 | HTML class | 图标 | 颜色 |
|----------|-----------|------|------|
| 普通文字文档（未加密） | 无 | `doc1.png` (CDN) | 浅蓝 |
| 加密文档 | `doc-suo` | `doc2.png` (CDN) | 白/灰 + 锁 |
| 图片文档（未加密） | `doc-pic` | 缩略图 `<img>` | — |
| 图片文档（加密） | `doc-suo` | `doc2.png`（隐藏缩略图）| 白/灰 + 锁 |

文件夹图标：
- 普通文件夹：`folder1.png` (CDN)
- 加密文件夹：`folder2.png` (CDN)

## 数据格式

### 文件夹

放在 `mockData.folders` 数组中：

```js
{ id: '唯一ID', name: '显示名', icon: FOLDER_ICON, locked: false, password: '' }
```

- `locked: true` → 需要密码才能进入，密码由 `password` 字段验证
- 加密文件夹图标用 `FOLDER_ICON_LOCKED`

### 文档

放在 `mockData.documents` 对象中，key 是文件夹 id：

```js
documents: {
  文件夹id: [
    {
      id: '唯一ID',
      name: '文档名',
      type: 'text',      // 'text' 或 'image'
      locked: false,     // true=需要文档密码
      password: '',      // 加密文档的密码
      src: '',           // 图片文档的图片URL
      content: '<h2>标题</h2><p>正文</p>...'  // 文字文档的HTML内容
    }
  ]
}
```

### 文档内容支持的 HTML 标签

- `<h2>` — 主标题
- `<h3><i></i>章节标题</h3>` — 章节标题（`<i>` 自动渲染蓝色菱形图标）
- `<p>` — 正文
- `<table class="doc-table">` — 表格（无边框，蓝底表头，交替行背景）
  - `<th>` 表头
  - `<td>` 单元格
  - `<td class="col1">` 绿色文字
  - `<td class="col2">` 橙色文字
  - `<td class="col3">` 红色文字
  - `<td class="copy-code">` 可点击复制（显示 toast "已复制"）
- `<ul><li>` — 列表（蓝色圆点）
- `<img>` — 图片
- `<br>` — 换行

## 添加新内容

1. 在 `mockData.folders` 数组末尾加文件夹
2. 在 `mockData.documents` 对象中加对应的文档数组
3. 刷新浏览器

示例：添加一个公开文件夹

```js
// folders 数组：
{ id:'newfolder', name:'新文件夹', icon: FOLDER_ICON, locked:false, password:'' }

// documents 对象：
newfolder: [
  {
    id:'doc1', name:'文档一', type:'text', locked:false, password:'', src:'',
    content: '<h2>文档一</h2><p>内容...</p>'
  }
]
```

## 改枪码文档特性

改枪码使用了 `copy-code` class，点击代码自动复制到剪贴板，屏幕底部弹出白色 toast "已复制"。
