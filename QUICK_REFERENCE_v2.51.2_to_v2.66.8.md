# Arco Design 升级快速参考指南

## v2.51.2 → v2.66.8

本文档提供快速查找和解决常见升级问题的方法。

---

## 🔴 高风险变更速查

### 1. Carousel 卡片模式样式完全重构

**症状**：
- 卡片动画异常
- 卡片位置错乱
- 透明度不正确

**快速检查**：
```bash
grep -r "\.arco-carousel-card" src/
```

**快速修复**：
```css
/* 临时禁用自定义样式，使用默认样式 */
/* .arco-carousel-card { ... } */

/* 或者根据新结构重写 */
.arco-carousel-card.arco-carousel-horizontal {
  /* 水平模式样式 */
}

.arco-carousel-card.arco-carousel-vertical {
  /* 垂直模式样式 */
}
```

---

### 2. Avatar display 属性变更

**症状**：
- 头像对齐异常
- 头像与文本基线不对齐
- 在 flex 容器中位置错误

**快速检查**：
```bash
# 查找头像使用
grep -r "<Avatar" src/
grep -r "\.arco-avatar" src/
```

**快速修复**：
```css
/* 方案 1：恢复旧的 display 值 */
.arco-avatar-image {
  display: inline-block !important;
}

/* 方案 2：调整父容器 */
.your-container {
  display: flex;
  align-items: center;
}
```

---

## ⚠️ 中等风险变更速查

### 1. Cascader showSearch.renderOption 签名变更

**症状**：
- TypeScript 编译错误
- 搜索结果渲染异常

**快速检查**：
```bash
grep -rn "showSearch.*renderOption" src/
```

**快速修复**：
```typescript
// 旧代码
showSearch={{
  renderOption: (inputValue, option) => {
    return <div>{option.label}</div>;
  }
}}

// 新代码 - 添加第三个参数
showSearch={{
  renderOption: (inputValue, option, extraOptions) => {
    const { checked } = extraOptions || {};
    return (
      <div className={checked ? 'checked' : ''}>
        {option.label}
      </div>
    );
  }
}}
```

---

### 2. Select 大尺寸字体变更

**症状**：
- 大尺寸 Select 字体变小（改为 14px）

**快速检查**：
```bash
grep -rn "size=\"large\"" src/ | grep Select
```

**快速修复**：
```css
.arco-select-size-large .arco-select-view-value {
  font-size: 16px; /* 恢复或自定义大小 */
}
```

---

### 3. Button 组样式变更

**症状**：
- 按钮组中单个按钮的圆角异常

**快速检查**：
```bash
grep -rn "Button.Group" src/
```

**快速修复**：
```css
/* 通常不需要修复，新版本已自动处理 */
/* 如果有样式冲突，检查是否有 !important 覆盖 */
```

---

## 📦 新组件使用指南

### ColorPicker（颜色选择器）

**最简使用**：
```tsx
import { ColorPicker } from '@arco-design/web-react';

<ColorPicker defaultValue="#165DFF" onChange={(v) => console.log(v)} />
```

**渐变色模式**：
```tsx
<ColorPicker
  mode={['single', 'gradient']}
  defaultValue={[
    { color: '#165DFF', percent: 0 },
    { color: '#722ED1', percent: 100 },
  ]}
/>
```

---

### VerificationCode（验证码输入）

**最简使用**：
```tsx
import { VerificationCode } from '@arco-design/web-react';

<VerificationCode
  length={6}
  onChange={(value) => console.log(value)}
/>
```

---

### Watermark（水印）

**最简使用**：
```tsx
import { Watermark } from '@arco-design/web-react';

<Watermark content="机密文档">
  <div>{/* 你的内容 */}</div>
</Watermark>
```

---

## 🔧 新功能快速应用

### 1. DatePicker inputProps

**用途**：自定义输入框行为

```tsx
<DatePicker
  inputProps={{
    placeholder: '自定义占位符',
    onFocus: () => console.log('focus'),
    onBlur: () => console.log('blur'),
  }}
/>
```

---

### 2. Input/Select autoWidth

**用途**：输入框和选择器宽度自适应

```tsx
// 简单用法
<Input autoWidth placeholder="输入内容" />
<Select autoWidth options={options} />

// 高级用法 - 限制最小和最大宽度
<Input
  autoWidth={{
    minWidth: 100,
    maxWidth: 300,
  }}
  placeholder="输入内容"
/>

<Select
  autoWidth={{
    minWidth: 150,
    maxWidth: 400,
  }}
  options={options}
/>
```

---

### 3. Select maxTagCount

**用途**：限制多选标签显示数量

```tsx
<Select
  mode="multiple"
  maxTagCount={3}
  options={options}
/>

// 或高级用法
<Select
  mode="multiple"
  maxTagCount={{
    count: 2,
    render: (count) => `+${count} 更多`,
    popover: { placement: 'top' },
  }}
/>
```

---

### 4. Typography tooltipProps

**用途**：自定义省略号 tooltip

```tsx
<Typography.Paragraph
  ellipsis={{
    rows: 2,
    tooltipProps: {
      position: 'top',
      color: 'blue',
    },
  }}
>
  长文本...
</Typography.Paragraph>
```

---

### 5. Cascader 自定义图标

**用途**：自定义级联选择器图标

```tsx
import { IconLoading, IconCheck, IconRight } from '@arco-design/web-react/icon';

<Cascader
  icons={{
    loading: <IconLoading />,
    checked: <IconCheck />,
    next: <IconRight />,
  }}
/>
```

---

### 6. Input 输入法事件

**用途**：处理中文输入法

```tsx
<Input
  onCompositionStart={(e) => console.log('输入法开始')}
  onCompositionEnd={(e) => console.log('输入法结束')}
/>
```

---

### 7. InputNumber onChange reason

**用途**：区分输入来源

```tsx
<InputNumber
  onChange={(value, reason) => {
    console.log('值:', value);
    console.log('来源:', reason); // 'userInput' | 'buttonClick' | 'arrowKey'
  }}
/>
```

---

### 8. Menu onEllipsisChange

**用途**：监听水平菜单省略状态

```tsx
<Menu
  mode="horizontal"
  onEllipsisChange={(ellipsis) => {
    console.log('菜单是否省略:', ellipsis);
  }}
/>
```

---

### 9. Slider 多点支持

**用途**：多个滑块点

```tsx
<Slider
  value={[20, 40, 60, 80]}
  onChange={(value) => console.log(value)}
/>
```

---

### 10. Tree animation

**用途**：禁用树形控件动画

```tsx
<Tree animation={false} />
```

---

### 11. Upload method

**用途**：自定义 HTTP 方法

```tsx
<Upload
  method="PUT"  // 默认是 POST
  action="/api/upload"
/>
```

---

## 🐛 常见问题速查

### Q: Table 排序异常，undefined 值处理不当

**症状**：包含 undefined/null 值的列排序时出错

**解决**：
```typescript
// 确保 sorter 函数处理 undefined/null
sorter: (a, b) => {
  const valA = a.field ?? '';
  const valB = b.field ?? '';
  return valA > valB ? 1 : -1;
}
```

---

### Q: Form 依赖关系不工作

**症状**：字段依赖更新不触发

**解决**：
```typescript
// 检查是否有循环依赖
// 错误示例
<Form.Item name="a" dependencies={['b']}>...</Form.Item>
<Form.Item name="b" dependencies={['a']}>...</Form.Item>

// 正确示例
<Form.Item name="a">...</Form.Item>
<Form.Item name="b" dependencies={['a']}>...</Form.Item>
```

---

### Q: Input suffix 和 addAfter 不能同时使用

**症状**：v2.51.2 中不能同时使用，导致只显示一个

**解决**：升级到 v2.66.8 后此问题已修复，可以同时使用

```tsx
<Input
  suffix={<IconSearch />}
  addAfter="搜索"
/>
```

---

### Q: Select allowCreate 导致无限循环

**症状**：使用 allowCreate 时组件卡死

**解决**：v2.66.8 已修复，确保升级后重新测试

---

### Q: Safari 中 Input 清除按钮导致抖动

**症状**：Safari 中点击清除按钮时输入框抖动

**解决**：v2.66.8 已修复此问题

---

### Q: Table 虚拟滚动时展开行不工作

**症状**：开启虚拟滚动后，expandedRows 或 treeData 不显示

**解决**：v2.66.8 已支持，确保升级后重新测试

---

## 📝 样式覆盖冲突速查

### 查找项目中的样式覆盖

```bash
# 查找所有 Arco 样式覆盖
grep -r "\.arco-" src/ --include="*.css" --include="*.less" --include="*.scss"

# 查找特定组件的样式覆盖
grep -r "\.arco-carousel" src/
grep -r "\.arco-avatar" src/
grep -r "\.arco-btn" src/
grep -r "\.arco-select" src/
grep -r "\.arco-table" src/
grep -r "\.arco-form" src/
grep -r "\.arco-input" src/
grep -r "\.arco-tabs" src/
```

---

### 高优先级样式冲突处理

如果样式覆盖不生效，使用更具体的选择器：

```css
/* 不推荐：可能不够具体 */
.arco-btn {
  color: red;
}

/* 推荐：使用更具体的选择器 */
.your-page .your-container .arco-btn {
  color: red;
}

/* 必要时：使用 !important（最后手段）*/
.your-page .arco-btn {
  color: red !important;
}
```

---

## 🔍 TypeScript 类型错误速查

### 常见类型错误和解决方案

**错误 1：Cascader renderOption 类型不匹配**
```typescript
// 错误
Type '(inputValue: string, option: NodeProps<T>) => ReactNode' is not assignable to type...

// 解决：添加第三个参数
(inputValue: string, option: NodeProps<T>, extraOptions?: { checked: boolean }) => ReactNode
```

---

**错误 2：找不到类型导出**
```typescript
// 错误
Module '"@arco-design/web-react"' has no exported member 'MessageHookReturnType'

// 解决：确认导入路径
import { Message, MessageHookReturnType } from '@arco-design/web-react';
```

---

**错误 3：InputNumber onChange 类型不匹配**
```typescript
// 旧类型
onChange?: (value: number) => void

// 新类型
onChange?: (value: number, reason?: 'userInput' | 'buttonClick' | 'arrowKey') => void

// 解决：更新函数签名
<InputNumber onChange={(value, reason) => { /* ... */ }} />
```

---

## 📊 性能对比命令

### 构建产物大小对比

```bash
# 升级前
npm run build
du -sh dist/
# 记录输出

# 升级后
npm run build
du -sh dist/
# 记录输出

# 详细对比
ls -lh dist/*.js | awk '{print $5, $9}'
```

---

### 运行时性能测试

```javascript
// 在浏览器控制台执行
// 测试组件渲染性能
console.time('render');
// 执行操作（如滚动 Table）
console.timeEnd('render');

// 测试内存使用
console.memory
```

---

## 🚀 快速回滚方案

如果升级后出现严重问题需要回滚：

```bash
# 1. 恢复 package.json
cp package.json.backup package.json

# 2. 恢复 lock 文件
cp yarn.lock.backup yarn.lock
# 或
cp package-lock.json.backup package-lock.json

# 3. 重新安装依赖
rm -rf node_modules
npm install
# 或
yarn install

# 4. 重新构建
npm run build

# 5. 部署旧版本
npm run deploy
```

---

## 📞 获取帮助

### 官方资源

- **文档**：https://arco.design/
- **GitHub Issues**：https://github.com/arco-design/arco-design/issues
- **GitHub Discussions**：https://github.com/arco-design/arco-design/discussions

### 搜索已知问题

```bash
# 在 GitHub 上搜索相关问题
# https://github.com/arco-design/arco-design/issues?q=is%3Aissue+[关键词]
```

---

## 🎯 测试优先级建议

### 优先级 1（必测）
1. ✅ 项目中所有使用 Carousel 卡片模式的页面
2. ✅ 项目中所有使用 Avatar 的页面
3. ✅ 项目中所有使用 Cascader showSearch 的表单
4. ✅ 项目中所有使用 Table 排序的页面
5. ✅ 项目中所有使用 Form dependencies 的表单

### 优先级 2（建议测试）
1. ✅ 所有使用 Select 的页面（特别是大尺寸）
2. ✅ 所有使用 DatePicker 的表单
3. ✅ 所有使用 Input 的表单（特别是 Safari）
4. ✅ 所有使用 Tabs 的页面
5. ✅ 所有使用 Button.Group 的页面

### 优先级 3（可选测试）
1. ✅ 其他使用 Arco 组件的页面
2. ✅ 响应式布局页面
3. ✅ 打印样式（如有）

---

## 📅 升级时间估算

- **小型项目**（< 50 个 Arco 组件使用）：1-2 天
- **中型项目**（50-200 个 Arco 组件使用）：2-5 天
- **大型项目**（> 200 个 Arco 组件使用）：5-10 天

*以上时间包括测试和修复时间*

---

## ✅ 升级成功标志

- [ ] 所有 TypeScript 编译通过
- [ ] 所有页面正常显示
- [ ] 所有功能正常工作
- [ ] 样式与升级前一致（或按预期变化）
- [ ] 性能无明显下降
- [ ] 所有浏览器测试通过
- [ ] 自动化测试通过

---

**文档版本**: 1.0  
**更新日期**: 2026-01-09  
**适用版本**: Arco Design v2.51.2 → v2.66.8

---

**提示**：建议打印或收藏本文档，在升级过程中随时查阅。
