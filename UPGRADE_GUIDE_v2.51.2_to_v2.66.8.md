# Arco Design 升级指南：从 v2.51.2 到 v2.66.8

## 概述

本文档详细分析了从 Arco Design v2.51.2 升级到 v2.66.8 的所有变化，包括新增功能、API 变更、样式变更和潜在的破坏性变化。

### 升级概况

- **版本跨度**: v2.51.2 → v2.66.8（跨越 15 个次版本）
- **提交数量**: 433 个提交
- **文件变更**: 732 个文件被修改
- **代码变更**: +32,126 行新增，-4,856 行删除
- **Bug 修复**: 161+ 个修复
- **新特性**: 50+ 个新特性
- **组件总数**: 从 73 个增加到 77 个（新增 4 个）

---

## 一、新增组件

### 1. ColorPicker（颜色选择器）
**版本**: v2.47.0+

**功能特性**:
- 支持单色和渐变色模式
- 支持 HEX 和 RGB 格式
- 可选透明度通道
- 历史颜色和预设颜色
- 自定义触发元素
- 完整的受控/非受控模式

**主要 API**:
```typescript
interface ColorPickerProps {
  value?: string | GradientColor[];
  defaultValue?: string | GradientColor[];
  format?: 'hex' | 'rgb';
  mode?: 'single' | 'gradient' | ['single', 'gradient'];
  disabled?: boolean;
  size?: 'mini' | 'small' | 'default' | 'large';
  disabledAlpha?: boolean;
  showText?: boolean;
  showHistory?: boolean;
  showPreset?: boolean;
  historyColors?: string[];
  presetColors?: string[];
  triggerElement?: ReactNode | ((params) => ReactNode);
  onChange?: (value: string | GradientColor[]) => void;
  onVisibleChange?: (visible: boolean) => void;
  renderFooter?: () => ReactNode;
}
```

**迁移建议**:
- 如果项目中有自定义的颜色选择器，可以考虑迁移到官方组件
- 注意渐变色模式下的数据格式变化

### 2. VerificationCode（验证码输入框）
**版本**: v2.54.0+

**功能特性**:
- 专门用于验证码输入的组件
- 支持数字/字母输入
- 支持自定义长度
- 支持自动聚焦和粘贴功能

**迁移建议**:
- 如果项目中使用多个 Input 组件来实现验证码输入，建议迁移到此组件

### 3. Watermark（水印）
**版本**: v2.61.0+

**功能特性**:
- 给页面或组件添加水印
- 支持文字和图片水印
- 防删除机制

**迁移建议**:
- 如果项目中有自定义水印实现，可以考虑替换为官方组件

### 4. _hooks（内部 Hooks 目录）
**版本**: 多个版本持续添加

**功能特性**:
- 导出了一些内部 Hooks 供高级用户使用
- 包括 `usePersistCallback`、`useUpdate` 等

---

## 二、重要 API 和 Props 变更

### 1. Cascader（级联选择器）

**新增 Props**:
- `icons` (v2.50.0): 自定义图标配置
  ```typescript
  icons?: {
    loading?: ReactNode;
    checked?: ReactNode;
    next?: ReactNode;
  }
  ```
- `showSearch.renderOption` 参数增强: 新增第三个参数 `extraOptions`，包含 `checked` 状态

**接口变更**:
- 移除了 `allowCreate` 的限制（从 `Omit<SelectViewCommonProps, 'allowCreate' | 'showSearch'>` 改为 `Omit<SelectViewCommonProps, 'showSearch'>`）

**影响评估**: ⚠️ 中等
- 如果使用了自定义的 `showSearch.renderOption`，需要检查函数签名
- 如果之前依赖 `allowCreate` 不可用的行为，需要重新测试

### 2. Carousel（轮播图）

**新增特性**:
- 支持垂直卡片模式 (v2.56.0)
- 新增 `indicatorPosition: 'outer-right'` 选项

**接口变更**:
```typescript
// v2.51.2
indicatorPosition?: 'bottom' | 'top' | 'left' | 'right' | 'outer';

// v2.66.8
indicatorPosition?: 'bottom' | 'top' | 'left' | 'right' | 'outer' | 'outer-right';
```

**样式变更**: 🔴 高风险
- 大量样式重构，特别是卡片模式的动画
- 新增 RTL（从右到左）支持
- 垂直模式下的样式完全重写

**影响评估**: 🔴 高
- 如果 override 了 Carousel 的样式，特别是卡片模式，需要仔细测试
- 动画相关的样式需要重新验证

### 3. Table（表格）

**主要修复**:
- 修复了 `sortOrder` 处理 undefined 值的问题 (v2.66.8)
- 支持继承 'mini' 尺寸 (v2.66.2)
- 防止删除后行的意外重新渲染 (v2.66.0)
- 修复 `scrollbar-color` 属性导致的宽度计算问题 (v2.60.0)
- 虚拟列表支持 `expandedRows` 和 `treeData` (v2.60.2)

**新增特性**:
- RTL 支持 (v2.61.0)
- 改进的虚拟滚动性能

**样式变更**: ⚠️ 中等
- RTL 相关样式添加
- 滚动条处理逻辑优化

**影响评估**: ⚠️ 中等
- 如果使用了自定义排序逻辑，需要测试 undefined 值的处理
- 如果 override 了表格样式，需要检查是否与新样式冲突
- 虚拟滚动使用场景需要重新测试

### 4. Form（表单）

**新增 API**:
- 导出 `FormContext` (v2.66.5)
- `validateOnly` 支持 (v2.59.0): 只验证不触发状态更新

**主要修复**:
- 修复 dependencies 重叠检查问题 (v2.66.0)
- 修复 RTL 模式问题 (v2.61.3)
- 修复提交状态问题 (v2.61.3)
- 修复 rules 未设置且 validateOnly 为 true 时的报错 (v2.61.0)

**影响评估**: ⚠️ 中等
- 如果使用了复杂的表单依赖关系，需要重新测试
- validateOnly 功能可以优化现有的表单验证流程

### 5. Select（选择器）

**新增 Props**:
- `maxTagCount` (v2.63.0): 支持最大标签数量
- 新增 `popover` 属性用于自定义超出标签的弹出框 (v2.66.0+)

**主要修复**:
- 修复标签渲染问题 (v2.66.0)
- 修复 allowCreate 为 true 时的无限循环 (v2.58.2)
- 修复 allowCreate 时 value 设为 undefined 的错误 (v2.58.1)

**样式变更**:
- 大尺寸 Select 的字体大小从其他值改为 14px (v2.60.2)

**影响评估**: ⚠️ 中等
- 如果 override 了 Select 的字体样式，需要检查大尺寸的显示
- 使用 allowCreate 的场景需要重新测试

### 6. DatePicker（日期选择器）

**新增 Props**:
- `inputProps` (v2.60.0): 支持传递所有 Input 的 props
- `fixedTime` (v2.66.8): 范围选择器的固定时间属性
- 支持使用 locale 自定义格式 (v2.59.0)

**样式变更**:
- 聚焦时重用了统一的聚焦样式

**影响评估**: ⚠️ 低-中等
- 如果需要自定义输入框行为，可以使用新的 `inputProps`
- 默认占位符文本变更（移除了 "Please"）

### 7. Image（图片）

**新增 Props**:
- `renderImages` (v2.57.0): 自定义图片列表渲染
- `imageRender` (v2.57.0): 自定义图片渲染
- 修复 `previewProps` 问题 (v2.57.0)

**影响评估**: ⚠️ 低
- 如果使用了图片预览功能，建议测试 previewProps

### 8. InputTag（标签输入框）

**新增 Props**:
- `maxTagCount` (v2.57.0): 限制最大标签数量

**影响评估**: ⚠️ 低
- 如果需要限制标签数量，可以使用新 API

### 9. Typography（排版）

**新增 Props**:
- `tooltipProps` (v2.56.0): 自定义 tooltip 属性

**影响评估**: ⚠️ 低
- 如果需要自定义省略号的 tooltip，可以使用新 API

### 10. Menu（菜单）

**新增 Props**:
- `onEllipsisChange` (v2.56.0): 水平菜单省略号状态变化回调

**影响评估**: ⚠️ 低
- 如果需要响应菜单省略状态变化，可以使用新回调

### 11. Slider（滑块）

**新增特性**:
- 支持多个滑块点 (v2.61.0)

**主要修复**:
- 修复从 undefined 读取属性导致的错误

**影响评估**: ⚠️ 低-中等
- 如果使用双滑块或需要多点滑块，可以使用新功能

### 12. InputNumber（数字输入框）

**新增 Props**:
- `onChange` 回调新增 `reason` 参数，通知触发原因 (v2.61.0)

**影响评估**: ⚠️ 低
- 如果需要区分 onChange 的触发原因，可以使用新参数

### 13. Input（输入框）

**新增特性**:
- 支持 `onCompositionEvent` (v2.61.0)
- 修复不能同时使用 `suffix` 和 `addAfter` 的问题 (v2.63.1)
- Input.TextArea 可以接受 ConfigProvider.componentConfig 的 props (v2.66.9)

**样式修复**:
- 修复 Safari 中清除文本时的垂直抖动 (v2.53.0)

**影响评估**: ⚠️ 低-中等
- 如果之前因为 bug 而调整了样式或用法，需要重新检查

### 14. ConfigProvider（全局配置）

**新增 Props**:
- `effectGlobalModal` (v2.60.0): 影响全局 Modal 的配置

**影响评估**: ⚠️ 低
- 如果使用全局配置，可以使用新 API 来配置 Modal

### 15. Collapse（折叠面板）

**主要修复**:
- 折叠面板的展开/收起动画和交互优化 (v2.66.0+)

**影响评估**: ⚠️ 低-中等
- 如果 override 了 Collapse 的动画样式，需要测试

### 16. Message（消息提示）

**新增特性**:
- `message.config` 支持 `closable` (v2.59.0): 可以配置消息是否可关闭

**影响评估**: ⚠️ 低
- 如果需要全局配置消息的可关闭性，可以使用新 API

### 17. Upload（上传）

**新增 Props**:
- 支持 `method` prop (v2.56.0): 自定义 HTTP 方法

**影响评估**: ⚠️ 低
- 如果需要使用非 POST 方法上传，可以使用新 API

### 18. Trigger（触发器）

**新增 Props**:
- `boundaryDistance` (v2.57.0): 边界距离配置

**主要修复**:
- 修复箭头样式问题 (v2.54.0)
- 增强 mergeProps 逻辑 (v2.56.0)

**影响评估**: ⚠️ 低-中等
- 如果使用了复杂的 Trigger 配置，建议重新测试
- 箭头样式的修复可能影响 override 的样式

### 19. Tree（树形控件）

**新增 Props**:
- `animation` (v2.66.0): 支持动画属性

**主要修复**:
- 修复样式问题 (v2.54.0)

**影响评估**: ⚠️ 低
- 如果需要禁用树形控件的动画，可以使用新 API

### 20. VirtualList（虚拟列表）

**新增 Props**:
- `onScroll` 新增参数，通知当前列表项索引 (v2.58.0)
- `wrapperChild` (v2.58.2): 包装子元素

**影响评估**: ⚠️ 低
- 如果使用虚拟列表，可以利用新参数优化滚动处理

---

## 三、样式变更分析

### 1. 全局样式变更

**聚焦样式统一** (v2.64.0+):
- 多个组件统一了聚焦时的样式
- 影响组件: DatePicker, RangePicker, Tab
- **风险**: 如果 override 了这些组件的 focus 样式，需要重新检查

### 2. 组件级样式变更

#### Button（按钮）
**变更内容**:
```less
// 新增单个按钮在按钮组中的圆角修复
.@{btn-prefix-cls}-size-@{size}:first-child:last-child {
  border-radius: ~'@{btn-size-@{size}-radius}';
}

.@{btn-prefix-cls}-size-@{size}.@{btn-prefix-cls}-shape-round:first-child:last-child {
  border-radius: calc(~'@{size-@{size}}' * 0.5);
}
```
- **影响**: 修复了按钮组中单个按钮的圆角显示
- **风险级别**: ⚠️ 低-中等
- **迁移建议**: 如果 override 了按钮组样式，需要检查单按钮的圆角

#### Avatar（头像）
**变更内容**:
```less
// 从 inline-block 改为 inline-flex
&-image {
  display: inline-flex; // 原来是 inline-block
  width: 100%;
  height: 100%;
}
```
- **影响**: 可能影响头像的对齐和布局
- **风险级别**: ⚠️ 中等
- **迁移建议**: 检查头像组件的布局，特别是在 flex 容器中的表现

#### Carousel（轮播图）
**变更内容**:
- 大量的卡片模式动画重写
- 新增垂直卡片模式的完整样式
- 新增 RTL 支持
- 新增 `outer-right` 指示器位置样式

**关键变更**:
```less
// 水平模式和垂直模式分离
&.@{carousel-prefix-cls}-horizontal { /* 水平模式样式 */ }
&.@{carousel-prefix-cls}-vertical { /* 垂直模式样式 */ }

// RTL 支持
&-rtl {
  direction: rtl;
}
```
- **影响**: 卡片模式的动画和布局完全重构
- **风险级别**: 🔴 高
- **迁移建议**: 
  - 仔细测试所有轮播图使用场景
  - 如果 override 了卡片模式样式，需要完全重写
  - 特别注意动画效果的变化

#### Transfer（穿梭框）
**变更内容**:
```less
style(Transfer): list item overflow
```
- **影响**: 列表项溢出处理
- **风险级别**: ⚠️ 低

#### Input（输入框）
**变更内容**:
- 修复 Safari 中的垂直抖动
- 优化清除按钮的样式

- **风险级别**: ⚠️ 低
- **迁移建议**: 在 Safari 中测试输入框的清除功能

#### Divider（分割线）
**变更内容**:
```less
enhance: divider line style inherit
```
- **影响**: 分割线样式继承优化
- **风险级别**: ⚠️ 低

#### Tabs（标签页）
**变更内容**:
```less
style: line tabs hide ink
style: tabs style in rtl & vertical
```
- **影响**: 线型标签页的墨水条隐藏，RTL 和垂直模式样式
- **风险级别**: ⚠️ 中等
- **迁移建议**: 如果使用了线型标签页或 RTL/垂直模式，需要测试

---

## 四、React 19 兼容性

**版本**: v2.66.0+

Arco Design 现在兼容 React 19，包括:
- 移除了 `findDOMNode` 的使用
- 更新了 React 19 的适配器
- 新增了 `es/_util/react-19-adapter.js`

**迁移建议**:
- 如果计划升级到 React 19，Arco v2.66.8 已经做好准备
- 如果仍在使用 React 16/17/18，无需担心兼容性问题

---

## 五、依赖变更

### 主要依赖更新

根据 package.json 的对比，主要依赖保持稳定，没有重大版本升级:

```json
{
  "dependencies": {
    "@arco-design/color": "^0.4.0",
    "@babel/runtime": "^7.5.5",
    "b-tween": "^0.3.3",
    "b-validate": "^1.4.2",
    "compute-scroll-into-view": "^1.0.17",
    "dayjs": "^1.10.5",
    "lodash": "^4.17.21",
    "number-precision": "^1.3.1",
    "react-focus-lock": "^2.13.2",
    "react-is": "^18.2.0", // 更新以支持 React 19
    "react-transition-group": "^4.3.0",
    "resize-observer-polyfill": "^1.5.1",
    "scroll-into-view-if-needed": "^2.2.20",
    "shallowequal": "^1.1.0"
  }
}
```

**风险评估**: ⚠️ 低
- 依赖更新幅度小，大部分保持不变
- `react-is` 更新到 v18.2.0 以支持 React 19

---

## 六、破坏性变更分析

### 1. 潜在破坏性变更

虽然这些版本都是次版本更新（遵循语义化版本），理论上不应该有破坏性变更，但以下情况可能导致兼容性问题：

#### 🔴 高风险区域

1. **Carousel 组件样式**
   - 卡片模式的动画和布局完全重构
   - 如果项目中 override 了这些样式，很可能需要重写

2. **Avatar 组件布局**
   - `display` 属性从 `inline-block` 改为 `inline-flex`
   - 可能影响在某些布局中的对齐方式

#### ⚠️ 中等风险区域

1. **Button 样式**
   - 按钮组中单个按钮的圆角处理变更
   - 如果 override 了按钮组样式，需要检查

2. **Select 字体大小**
   - 大尺寸 Select 的字体大小改为 14px
   - 如果依赖原来的字体大小，需要调整

3. **Table 组件**
   - 排序、滚动、虚拟化相关的多个修复
   - 如果使用了这些功能，需要重新测试

4. **Form 组件**
   - Dependencies 检查逻辑变更
   - 如果使用了复杂的表单依赖，需要测试

5. **Cascader 组件**
   - `showSearch.renderOption` 函数签名变更
   - 如果使用了自定义渲染，需要更新代码

### 2. 样式覆盖冲突

如果项目中使用了以下方式 override 样式，需要特别注意：

```css
/* 高风险样式覆盖 */
.arco-carousel-card { /* 完全重构，需要重写 */ }
.arco-avatar-image { /* display 属性变更 */ }
.arco-btn-group .arco-btn:first-child:last-child { /* 新增规则 */ }
.arco-select-size-large { /* 字体大小变更 */ }
.arco-tabs-line { /* 墨水条样式变更 */ }

/* 中等风险样式覆盖 */
.arco-table { /* 多个样式优化 */ }
.arco-form-item { /* 验证状态样式调整 */ }
.arco-input { /* Safari 兼容性修复 */ }
.arco-trigger-arrow { /* 箭头样式修复 */ }
```

---

## 七、升级步骤和检查清单

### 第一阶段：准备工作

- [ ] 1. 备份当前项目代码
- [ ] 2. 确保项目有完善的测试覆盖
- [ ] 3. 记录所有 override 的 Arco 组件样式
- [ ] 4. 记录所有使用的 Arco 组件和功能
- [ ] 5. 在独立分支进行升级测试

### 第二阶段：升级依赖

```bash
# 1. 更新 package.json 中的版本
npm install @arco-design/web-react@2.66.8
# 或
yarn add @arco-design/web-react@2.66.8

# 2. 清除缓存（如果需要）
rm -rf node_modules package-lock.json
npm install
```

### 第三阶段：代码调整

#### 1. 检查 Cascader 使用

如果使用了自定义的 `showSearch.renderOption`:

```typescript
// v2.51.2
showSearch={{
  renderOption: (inputValue, option) => {
    // ...
  }
}}

// v2.66.8 - 新增第三个参数
showSearch={{
  renderOption: (inputValue, option, extraOptions) => {
    // extraOptions.checked 表示是否已选中
    // ...
  }
}}
```

#### 2. 检查表单依赖

如果使用了 Form 的 dependencies 功能:

```typescript
// 检查是否有重叠的依赖关系
<Form.Item dependencies={['field1', 'field2']} />
```

#### 3. 使用新 API（可选）

```typescript
// 使用 DatePicker 的 inputProps
<DatePicker
  inputProps={{
    onFocus: () => {},
    onBlur: () => {},
  }}
/>

// 使用 Select 的 maxTagCount
<Select
  mode="multiple"
  maxTagCount={3}
  maxTagCount={{
    count: 3,
    render: (invisibleCount) => `+${invisibleCount}`,
  }}
/>

// 使用 ColorPicker
import { ColorPicker } from '@arco-design/web-react';

<ColorPicker
  defaultValue="#165DFF"
  onChange={(value) => console.log(value)}
/>
```

### 第四阶段：样式检查和修复

#### 1. 高优先级样式检查

```bash
# 检查项目中是否 override 了以下样式
grep -r "arco-carousel-card" src/
grep -r "arco-avatar-image" src/
grep -r "arco-btn-group" src/
grep -r "arco-select-size-large" src/
```

#### 2. Carousel 样式修复

如果 override 了 Carousel 的卡片模式样式，需要根据新的结构调整：

```less
// v2.66.8 新结构
.arco-carousel-card {
  &.arco-carousel-horizontal {
    // 水平模式样式
    .arco-carousel-item-prev { /* ... */ }
    .arco-carousel-item-next { /* ... */ }
    .arco-carousel-item-current { /* ... */ }
  }
  
  &.arco-carousel-vertical {
    // 垂直模式样式
  }
  
  &-rtl {
    // RTL 样式
  }
}
```

#### 3. Avatar 布局修复

如果 Avatar 的布局出现问题，可以临时使用以下修复：

```css
/* 如果需要保持旧的行为 */
.arco-avatar-image {
  display: inline-block; /* 覆盖回旧值 */
}

/* 或者调整父容器的布局 */
.your-avatar-container {
  display: flex;
  align-items: center;
}
```

#### 4. Button 组样式修复

如果单个按钮在按钮组中的圆角有问题：

```css
/* 检查是否有冲突的样式 */
.arco-btn-group .arco-btn:first-child:last-child {
  /* 新版本会自动添加 border-radius */
  /* 确保没有 !important 规则覆盖 */
}
```

### 第五阶段：功能测试

#### 1. 关键组件测试清单

- [ ] **Table 组件**
  - [ ] 排序功能（特别是 undefined 值）
  - [ ] 虚拟滚动
  - [ ] 展开行和树形数据
  - [ ] 固定列和滚动
  - [ ] mini 尺寸继承

- [ ] **Form 组件**
  - [ ] 表单验证
  - [ ] 字段依赖关系
  - [ ] RTL 模式（如果使用）
  - [ ] 提交和重置

- [ ] **Select 组件**
  - [ ] 多选模式的标签显示
  - [ ] allowCreate 功能
  - [ ] 大尺寸的显示效果
  - [ ] maxTagCount 功能

- [ ] **Cascader 组件**
  - [ ] 搜索功能
  - [ ] 自定义渲染
  - [ ] 多级联动

- [ ] **DatePicker 组件**
  - [ ] 日期选择
  - [ ] 范围选择
  - [ ] inputProps 功能
  - [ ] 自定义格式

- [ ] **Carousel 组件**
  - [ ] 普通模式
  - [ ] 卡片模式
  - [ ] 垂直模式
  - [ ] 指示器位置
  - [ ] 动画效果

- [ ] **Input 组件**
  - [ ] suffix 和 addAfter 同时使用
  - [ ] 清除功能（特别是 Safari）
  - [ ] 输入法事件

- [ ] **Image 组件**
  - [ ] 图片预览
  - [ ] previewProps 功能

#### 2. 浏览器兼容性测试

- [ ] Chrome/Edge (最新版本)
- [ ] Firefox (最新版本)
- [ ] Safari (最新版本) - 特别注意 Input 组件
- [ ] 移动端浏览器

#### 3. 样式回归测试

- [ ] 检查所有页面的视觉效果
- [ ] 特别注意 override 样式的组件
- [ ] 检查响应式布局
- [ ] 检查暗色主题（如果使用）
- [ ] 检查 RTL 模式（如果使用）

### 第六阶段：性能测试

- [ ] 页面加载时间对比
- [ ] 虚拟列表滚动性能
- [ ] 大数据表格性能
- [ ] 复杂表单渲染性能

### 第七阶段：上线准备

- [ ] 在测试环境充分测试
- [ ] 准备回滚方案
- [ ] 监控错误日志
- [ ] 灰度发布（如果可能）

---

## 八、常见问题和解决方案

### Q1: 升级后 Carousel 卡片模式动画异常

**原因**: 卡片模式的样式和动画完全重构

**解决方案**:
1. 移除所有自定义的 Carousel 卡片模式样式
2. 使用新版本的默认样式
3. 如果必须自定义，参考新版本的样式结构重写

```less
// 新版本结构
.arco-carousel-card.arco-carousel-horizontal {
  .arco-carousel-item-prev { }
  .arco-carousel-item-next { }
  .arco-carousel-item-current { }
}
```

### Q2: Avatar 组件对齐问题

**原因**: display 从 inline-block 改为 inline-flex

**解决方案**:
```css
/* 方案 1: 恢复旧的 display 值 */
.arco-avatar-image {
  display: inline-block !important;
}

/* 方案 2: 调整父容器布局 */
.avatar-container {
  display: flex;
  align-items: center;
}
```

### Q3: Select 大尺寸字体显示不符合预期

**原因**: 大尺寸 Select 的字体大小统一改为 14px

**解决方案**:
```css
.arco-select-size-large .arco-select-view-value {
  font-size: 16px; /* 或你需要的大小 */
}
```

### Q4: Table 排序功能异常

**原因**: sortOrder 对 undefined 值的处理逻辑变更

**解决方案**:
检查自定义的排序函数，确保正确处理 undefined 值：

```typescript
sorter: (a, b) => {
  // 确保处理 undefined/null 值
  const valA = a.field ?? '';
  const valB = b.field ?? '';
  return valA > valB ? 1 : -1;
}
```

### Q5: Form 依赖关系不工作

**原因**: dependencies 重叠检查逻辑优化

**解决方案**:
检查并优化表单依赖关系，避免循环依赖：

```typescript
// 确保依赖关系是单向的
<Form.Item name="field1" dependencies={['field2']}>
  {/* ... */}
</Form.Item>
<Form.Item name="field2">
  {/* 不要再依赖 field1 */}
</Form.Item>
```

### Q6: Cascader 搜索渲染报错

**原因**: showSearch.renderOption 函数签名变更

**解决方案**:
更新函数签名，添加第三个参数：

```typescript
showSearch={{
  renderOption: (inputValue, option, extraOptions) => {
    const { checked } = extraOptions;
    return (
      <div className={checked ? 'checked' : ''}>
        {option.label}
      </div>
    );
  }
}}
```

### Q7: 样式优先级冲突

**原因**: 新版本可能添加了更具体的选择器

**解决方案**:
1. 使用更具体的选择器
2. 使用 CSS 模块或 CSS-in-JS 来避免冲突
3. 必要时使用 `!important`（不推荐）

```css
/* 使用更具体的选择器 */
.your-container .arco-component .arco-element {
  /* 你的样式 */
}
```

---

## 九、性能优化建议

升级到 v2.66.8 后，可以利用以下新特性优化性能：

### 1. 使用新的 Hook

```typescript
import { usePersistCallback } from '@arco-design/web-react/_hooks';

// 避免不必要的重新渲染
const handleClick = usePersistCallback(() => {
  // ...
});
```

### 2. 虚拟列表优化

```typescript
<VirtualList
  onScroll={(scrollTop, { index }) => {
    // 使用新的 index 参数优化滚动处理
    console.log('当前显示的第一项索引:', index);
  }}
/>
```

### 3. Table 虚拟化

```typescript
<Table
  virtualized
  virtualListProps={{
    threshold: 100, // 根据数据量调整
  }}
  scroll={{ y: 500 }}
  data={largeDataSet}
/>
```

### 4. Form 验证优化

```typescript
<Form>
  <Form.Item
    validateOnly // 只验证，不触发状态更新
    rules={[{ required: true }]}
  >
    <Input />
  </Form.Item>
</Form>
```

---

## 十、新功能使用示例

### 1. ColorPicker 颜色选择器

```typescript
import { ColorPicker, Space } from '@arco-design/web-react';

function Demo() {
  const [color, setColor] = useState('#165DFF');

  return (
    <Space>
      {/* 基础用法 */}
      <ColorPicker
        defaultValue="#165DFF"
        onChange={setColor}
      />

      {/* 渐变色模式 */}
      <ColorPicker
        mode={['single', 'gradient']}
        defaultValue={[
          { color: '#165DFF', percent: 0 },
          { color: '#722ED1', percent: 100 },
        ]}
      />

      {/* 禁用透明度 */}
      <ColorPicker
        disabledAlpha
        showText
        format="hex"
      />

      {/* 自定义触发元素 */}
      <ColorPicker
        triggerElement={
          <div style={{ 
            width: 100, 
            height: 40, 
            backgroundColor: color,
            cursor: 'pointer',
            borderRadius: 4,
          }} />
        }
      />
    </Space>
  );
}
```

### 2. Select maxTagCount

```typescript
import { Select } from '@arco-design/web-react';

<Select
  mode="multiple"
  maxTagCount={{
    count: 2,
    render: (invisibleTagCount) => (
      <span>+{invisibleTagCount} 更多</span>
    ),
    popover: {
      // 自定义弹出框配置
      placement: 'top',
    },
  }}
  options={[...]}
/>
```

### 3. DatePicker inputProps

```typescript
import { DatePicker } from '@arco-design/web-react';

<DatePicker
  inputProps={{
    placeholder: '自定义占位符',
    onFocus: () => console.log('聚焦'),
    onBlur: () => console.log('失焦'),
    style: { width: 300 },
  }}
/>
```

### 4. Carousel 垂直卡片模式

```typescript
import { Carousel } from '@arco-design/web-react';

<Carousel
  animation="card"
  direction="vertical"
  indicatorPosition="outer-right"
  style={{ height: 400 }}
>
  <div>卡片 1</div>
  <div>卡片 2</div>
  <div>卡片 3</div>
</Carousel>
```

### 5. Typography tooltipProps

```typescript
import { Typography } from '@arco-design/web-react';

<Typography.Paragraph
  ellipsis={{
    rows: 2,
    expandable: true,
  }}
  tooltipProps={{
    position: 'top',
    color: 'blue',
  }}
>
  长文本内容...
</Typography.Paragraph>
```

---

## 十一、总结和建议

### 升级价值评估

**优点**:
- ✅ 161+ 个 bug 修复，提升稳定性
- ✅ 3 个新组件 (ColorPicker, VerificationCode, Watermark)
- ✅ 50+ 个新特性和 API 增强
- ✅ React 19 兼容性
- ✅ 更好的 RTL 支持
- ✅ 性能优化，特别是虚拟列表和表格

**风险点**:
- ⚠️ Carousel 卡片模式样式完全重构
- ⚠️ Avatar display 属性变更可能影响布局
- ⚠️ 多个组件的样式微调，如果有 override 需要测试
- ⚠️ 某些 API 签名变更（如 Cascader）

### 升级建议

**推荐升级的场景**:
1. 需要使用新组件（ColorPicker、Watermark 等）
2. 遇到了已修复的 bug
3. 需要更好的 React 19 兼容性
4. 需要新增的 API 功能

**谨慎升级的场景**:
1. 项目中大量 override 了 Arco 组件样式，特别是 Carousel
2. 项目处于关键迭代期，时间紧张
3. 缺乏充分的测试覆盖

**升级时间建议**:
- 预留 2-5 天进行测试和调整
- 优先在开发/测试环境验证
- 建议在版本迭代期进行，避免紧急修复

### 最终建议

鉴于有 433 个提交和大量的改进，建议升级，但需要：

1. **充分测试**: 特别是 override 了样式的组件
2. **分阶段**: 可以先升级到中间版本（如 v2.58.0），然后再升级到 v2.66.8
3. **准备回滚**: 确保有快速回滚方案
4. **监控**: 升级后密切关注错误日志和用户反馈

---

## 附录

### A. 版本里程碑

- **v2.52.0**: 多个组件 bug 修复
- **v2.54.0**: Trigger 箭头样式修复，Tree 样式修复
- **v2.56.0**: Typography tooltipProps，Menu onEllipsisChange
- **v2.57.0**: Image 自定义渲染，InputTag maxTagCount
- **v2.58.0**: VirtualList 改进，Select bug 修复
- **v2.59.0**: Form validateOnly，Message.config closable
- **v2.60.0**: DatePicker inputProps，ConfigProvider effectGlobalModal
- **v2.61.0**: Tree animation，Slider 多点支持，表单多个修复
- **v2.62.0**: ColorPicker renderFooter
- **v2.63.0**: Select maxTagCount
- **v2.64.0**: 聚焦样式统一
- **v2.65.0**: Form flat 修复
- **v2.66.0**: React 19 兼容，FormContext 导出，多个组件改进
- **v2.66.8**: Table sortOrder 修复，RangePicker fixedTime

### B. 相关资源

- [Arco Design 官方文档](https://arco.design/)
- [GitHub 仓库](https://github.com/arco-design/arco-design)
- [在线演示](https://arco.design/react/components/)

### C. 技术支持

如果在升级过程中遇到问题：

1. 查看 [GitHub Issues](https://github.com/arco-design/arco-design/issues)
2. 在 [GitHub Discussions](https://github.com/arco-design/arco-design/discussions) 提问
3. 参考 [官方文档](https://arco.design/)

---

**文档版本**: 1.0  
**更新日期**: 2026-01-09  
**适用版本**: Arco Design v2.51.2 → v2.66.8
