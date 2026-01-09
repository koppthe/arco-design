# Arco Design API 差异详细对比：v2.51.2 → v2.66.8

## 目录

1. [新增组件导出](#新增组件导出)
2. [接口变更详细对比](#接口变更详细对比)
3. [样式类名变更](#样式类名变更)
4. [TypeScript 类型变更](#typescript-类型变更)
5. [组件 Props 详细对比](#组件-props-详细对比)

---

## 新增组件导出

### 1. ColorPicker（颜色选择器）

**导出变更**:
```typescript
// v2.66.8 新增
export type { ColorPickerProps } from './ColorPicker/interface';
export { default as ColorPicker } from './ColorPicker';
```

**完整接口定义**:
```typescript
interface ColorPickerProps {
  style?: CSSProperties;
  className?: string | string[];
  defaultValue?: string | GradientColor[];
  value?: string | GradientColor[];
  showText?: boolean;
  format?: 'hex' | 'rgb';
  mode?: 'single' | 'gradient' | ['single', 'gradient'];
  disabled?: boolean;
  size?: 'mini' | 'small' | 'default' | 'large';
  defaultPopupVisible?: boolean;
  popupVisible?: boolean;
  unmountOnExit?: boolean;
  triggerProps?: Partial<TriggerProps>;
  disabledAlpha?: boolean;
  showHistory?: boolean;
  historyColors?: string[];
  showPreset?: boolean;
  presetColors?: string[];
  triggerElement?: ReactNode | ((params: { value: string | GradientColor[] }) => ReactNode);
  onChange?: (value: string | GradientColor[]) => void;
  onVisibleChange?: (visible: boolean) => void;
  renderFooter?: () => ReactNode; // v2.62.0+
}

interface GradientColor {
  color: string;
  percent: number;
}
```

### 2. VerificationCode（验证码输入框）

**导出变更**:
```typescript
// v2.66.8 新增
export type { VerificationCodeProps } from './VerificationCode/interface';
export { default as VerificationCode } from './VerificationCode';
```

### 3. Watermark（水印）

**导出变更**:
```typescript
// v2.66.8 新增
export type { WatermarkProps } from './Watermark/interface';
export { default as Watermark } from './Watermark';
```

### 4. Hook 返回类型导出

**Message**:
```typescript
// v2.51.2
export type { MessageProps } from './Message/interface';

// v2.66.8 - 新增 Hook 返回类型
export type { MessageProps, MessageHookReturnType } from './Message/interface';
```

**Modal**:
```typescript
// v2.66.8 - 新增 Hook 返回类型
export type { ModalProps, ModalHookReturnType } from './Modal/interface';
```

**Notification**:
```typescript
// v2.66.8 - 新增 Hook 返回类型
export type { NotificationProps, NotificationHookReturnType } from './Notification/interface';
```

### 5. Table 实例类型导出

```typescript
// v2.66.8 - 新增 Table 实例类型
export type { TableInstance } from './Table/table';
```

---

## 接口变更详细对比

### 1. Carousel.interface.ts

**变更内容**:
```typescript
// v2.51.2
interface CarouselProps {
  indicatorPosition?: 'bottom' | 'top' | 'left' | 'right' | 'outer';
  // ...
}

// v2.66.8 - 新增 'outer-right' 选项
interface CarouselProps {
  indicatorPosition?: 'bottom' | 'top' | 'left' | 'right' | 'outer' | 'outer-right';
  // ...
}
```

**影响分析**:
- 如果使用了 TypeScript 的严格模式，新增的选项不会破坏现有代码
- 可以使用新的 `'outer-right'` 位置来放置指示器

---

### 2. Cascader.interface.ts

**重要变更 1: SelectViewCommonProps 的 Omit 范围**

```typescript
// v2.51.2
export interface CascaderProps<T = any>
  extends Omit<SelectViewCommonProps, 'allowCreate' | 'showSearch'> {
  // ...
}

// v2.66.8 - 移除了 allowCreate 的限制
export interface CascaderProps<T = any> 
  extends Omit<SelectViewCommonProps, 'showSearch'> {
  // ...
}
```

**影响分析**:
- Cascader 现在可能接受 `allowCreate` prop（如果它在 SelectViewCommonProps 中）
- 需要检查是否有意外的行为

**重要变更 2: showSearch.renderOption 签名变更**

```typescript
// v2.51.2
showSearch?: boolean | {
  panelMode?: 'cascader' | 'select';
  renderOption?: (inputValue: string, option: NodeProps<T>) => ReactNode;
  retainInputValue?: boolean;
  retainInputValueWhileSelect?: boolean;
};

// v2.66.8 - renderOption 新增第三个参数
showSearch?: boolean | {
  panelMode?: 'cascader' | 'select';
  renderOption?: (
    inputValue: string,
    option: NodeProps<T>,
    options: extraOptions  // 新增参数
  ) => ReactNode;
  retainInputValue?: boolean;
  retainInputValueWhileSelect?: boolean;
};

// 新增接口
export interface extraOptions {
  checked: boolean;
}
```

**迁移示例**:
```typescript
// v2.51.2 代码
<Cascader
  showSearch={{
    renderOption: (inputValue, option) => {
      return <div>{option.label}</div>;
    }
  }}
/>

// v2.66.8 更新后（向后兼容，但建议使用新参数）
<Cascader
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
/>
```

**重要变更 3: 新增 icons prop**

```typescript
// v2.66.8 新增 (v2.50.0+)
interface CascaderProps<T = any> {
  icons?: {
    loading?: ReactNode;
    checked?: ReactNode;
    next?: ReactNode;
  };
  // ...
}
```

**使用示例**:
```typescript
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

### 3. DatePicker 新增 Props

**inputProps 支持** (v2.60.0+):

```typescript
// 新增属性
interface DatePickerProps {
  inputProps?: Partial<InputProps>; // 新增
  // ...
}

interface RangePickerProps {
  inputProps?: Partial<InputProps>; // 新增
  // ...
}
```

**使用示例**:
```typescript
<DatePicker
  inputProps={{
    placeholder: '请选择日期',
    onFocus: () => console.log('focus'),
    onBlur: () => console.log('blur'),
    style: { width: 300 },
  }}
/>

<RangePicker
  inputProps={{
    onFocus: () => console.log('focus'),
    onBlur: () => console.log('blur'),
  }}
/>
```

**fixedTime 支持** (v2.66.8+):

```typescript
interface RangePickerProps {
  fixedTime?: boolean; // 新增
  // ...
}
```

---

### 4. Select 新增 Props

**autoWidth 支持** (v2.54.0+):

```typescript
interface SelectProps {
  autoWidth?: boolean | {
    minWidth?: CSSProperties['minWidth'];
    maxWidth?: CSSProperties['maxWidth'];
  };
  // ...
}
```

**使用示例**:
```typescript
// 简单用法 - 自适应宽度
<Select autoWidth options={options} />

// 高级用法 - 限制最小和最大宽度
<Select
  autoWidth={{
    minWidth: 100,
    maxWidth: 300,
  }}
  options={options}
/>
```

**maxTagCount 支持** (v2.63.0+):

```typescript
interface SelectProps {
  maxTagCount?: number | {
    count?: number;
    render?: (invisibleTagCount: number) => ReactNode;
    popover?: {
      // Popover 的配置
      placement?: string;
      // ... 其他 Popover props
    };
  };
  // ...
}
```

**使用示例**:
```typescript
// 简单用法
<Select mode="multiple" maxTagCount={3} />

// 高级用法
<Select
  mode="multiple"
  maxTagCount={{
    count: 2,
    render: (count) => <span>+{count}</span>,
    popover: {
      placement: 'top',
    },
  }}
/>
```

---

### 5. Image 新增 Props

**renderImages 和 imageRender** (v2.57.0+):

```typescript
interface ImagePreviewGroupProps {
  renderImages?: (originalNode: ReactNode[]) => ReactNode; // 新增
  // ...
}

interface ImageProps {
  imageRender?: (originalNode: ReactNode) => ReactNode; // 新增
  // ...
}
```

---

### 6. Typography 新增 Props

**tooltipProps** (v2.56.0+):

```typescript
interface TypographyEllipsisConfig {
  tooltipProps?: Partial<TooltipProps>; // 新增
  // ...
}
```

**使用示例**:
```typescript
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

### 7. InputTag 新增 Props

**maxTagCount** (v2.57.0+):

```typescript
interface InputTagProps {
  maxTagCount?: number; // 新增
  // ...
}
```

---

### 8. InputNumber onChange 签名变更

**reason 参数** (v2.61.0+):

```typescript
// v2.51.2
interface InputNumberProps {
  onChange?: (value: number) => void;
}

// v2.66.8 - 新增 reason 参数
interface InputNumberProps {
  onChange?: (value: number, reason?: 'userInput' | 'buttonClick' | 'arrowKey') => void;
}
```

---

### 9. Input 新增 Props 和事件支持

**autoWidth 支持** (v2.54.0+):

```typescript
interface InputProps {
  autoWidth?: boolean | {
    minWidth?: CSSProperties['minWidth'];
    maxWidth?: CSSProperties['maxWidth'];
  };
  // ...
}
```

**使用示例**:
```typescript
// 简单用法 - 自适应宽度
<Input autoWidth placeholder="输入内容" />

// 高级用法 - 设置最小和最大宽度
<Input
  autoWidth={{
    minWidth: 100,
    maxWidth: 300,
  }}
  placeholder="输入内容"
/>
```

**onCompositionEvent** (v2.61.0+):

```typescript
interface InputProps {
  onCompositionStart?: React.CompositionEventHandler<HTMLInputElement>; // 新增
  onCompositionUpdate?: React.CompositionEventHandler<HTMLInputElement>; // 新增
  onCompositionEnd?: React.CompositionEventHandler<HTMLInputElement>; // 新增
  // ...
}
```

---

### 10. Menu 新增 Props

**onEllipsisChange** (v2.56.0+):

```typescript
interface MenuProps {
  onEllipsisChange?: (ellipsis: boolean) => void; // 新增
  // ...
}
```

**使用示例**:
```typescript
<Menu
  mode="horizontal"
  onEllipsisChange={(ellipsis) => {
    console.log('菜单省略状态:', ellipsis);
  }}
/>
```

---

### 11. Slider 新增特性

**多点支持** (v2.61.0+):

```typescript
// 现在 value 可以是数组，支持多个滑块点
interface SliderProps {
  value?: number | number[]; // 扩展支持数组
  defaultValue?: number | number[]; // 扩展支持数组
  // ...
}
```

---

### 12. Tree 新增 Props

**animation** (v2.66.0+):

```typescript
interface TreeProps {
  animation?: boolean; // 新增
  // ...
}
```

---

### 13. Upload 新增 Props

**method** (v2.56.0+):

```typescript
interface UploadProps {
  method?: 'POST' | 'PUT' | 'PATCH'; // 新增
  // ...
}
```

---

### 14. Trigger 新增 Props

**boundaryDistance** (v2.57.0+):

```typescript
interface TriggerProps {
  boundaryDistance?: number; // 新增
  // ...
}
```

---

### 15. VirtualList 增强

**onScroll 参数增强** (v2.58.0+):

```typescript
// v2.51.2
interface VirtualListProps {
  onScroll?: (scrollTop: number) => void;
}

// v2.66.8 - 新增 index 参数
interface VirtualListProps {
  onScroll?: (scrollTop: number, params: { index: number }) => void;
}
```

**wrapperChild** (v2.58.2+):

```typescript
interface VirtualListProps {
  wrapperChild?: (children: ReactNode) => ReactNode; // 新增
  // ...
}
```

---

### 16. ConfigProvider 新增 Props

**effectGlobalModal** (v2.60.0+):

```typescript
interface ConfigProviderProps {
  effectGlobalModal?: boolean; // 新增
  // ...
}
```

**componentConfig.textarea** (v2.66.9+):

```typescript
interface ConfigProviderProps {
  componentConfig?: {
    Input?: {
      TextArea?: Partial<TextAreaProps>; // 新增
    };
    // ... 其他组件配置
  };
  // ...
}
```

---

### 17. Form 新增功能

**FormContext 导出** (v2.66.5+):

```typescript
// 新增导出
export { FormContext } from './Form';
```

**validateOnly 支持** (v2.59.0+):

```typescript
// Form.Item 支持 validateOnly
interface FormItemProps {
  validateOnly?: boolean; // 新增
  // ...
}
```

---

### 18. Message 新增功能

**closable 配置** (v2.59.0+):

```typescript
Message.config({
  closable: true, // 新增：全局配置消息可关闭
  // ...
});
```

---

## 样式类名变更

### 1. Carousel 新增类名

```less
// 新增 RTL 类名
.arco-carousel-rtl { }

// 新增 horizontal 和 vertical 类名
.arco-carousel-horizontal { }
.arco-carousel-vertical { }

// 新增 outer-right 指示器类名
.arco-carousel-indicator-outer-right { }
```

### 2. Button 样式选择器优化

```less
// 新增单个按钮在按钮组中的选择器
.arco-btn-group .arco-btn-size-@{size}:first-child:last-child { }
.arco-btn-group .arco-btn-size-@{size}.arco-btn-shape-round:first-child:last-child { }
```

### 3. 各组件 RTL 样式

多个组件新增了 RTL 相关的样式类名，包括但不限于：
- `.arco-table-rtl`
- `.arco-form-rtl`
- `.arco-tabs-rtl`
- `.arco-carousel-rtl`

---

## TypeScript 类型变更

### 1. 更严格的类型定义

多个组件的 TypeScript 类型定义变得更加严格和准确：

**Table**:
```typescript
// 导出 TableInstance 类型
export type { TableInstance } from './Table/table';
```

**Hook 返回类型**:
```typescript
export type { MessageHookReturnType } from './Message/interface';
export type { ModalHookReturnType } from './Modal/interface';
export type { NotificationHookReturnType } from './Notification/interface';
```

### 2. 泛型改进

多个组件的泛型定义得到改进，提供更好的类型推导。

---

## 组件 Props 详细对比表

### 主要组件 Props 变更汇总

| 组件 | 新增 Props | 变更 Props | 版本 |
|------|-----------|-----------|------|
| **Cascader** | `icons` | `showSearch.renderOption` 签名变更 | v2.50.0+ |
| **Carousel** | - | `indicatorPosition` 新增 'outer-right' | v2.56.0+ |
| **ColorPicker** | 整个组件为新增 | - | v2.47.0+ |
| **DatePicker** | `inputProps`, `fixedTime` | - | v2.60.0+ |
| **Select** | `autoWidth`, `maxTagCount` | - | v2.54.0+, v2.63.0+ |
| **Image** | `renderImages`, `imageRender` | - | v2.57.0+ |
| **Typography** | `tooltipProps` | - | v2.56.0+ |
| **InputTag** | `maxTagCount` | - | v2.57.0+ |
| **InputNumber** | - | `onChange` 新增 reason 参数 | v2.61.0+ |
| **Input** | `autoWidth`, `onCompositionStart/Update/End` | - | v2.54.0+, v2.61.0+ |
| **Menu** | `onEllipsisChange` | - | v2.56.0+ |
| **Slider** | - | `value` 支持数组（多点） | v2.61.0+ |
| **Tree** | `animation` | - | v2.66.0+ |
| **Upload** | `method` | - | v2.56.0+ |
| **Trigger** | `boundaryDistance` | - | v2.57.0+ |
| **VirtualList** | `wrapperChild` | `onScroll` 新增 index 参数 | v2.58.0+ |
| **ConfigProvider** | `effectGlobalModal` | - | v2.60.0+ |
| **Form** | 导出 `FormContext` | - | v2.66.5+ |
| **VerificationCode** | 整个组件为新增 | - | v2.54.0+ |
| **Watermark** | 整个组件为新增 | - | v2.61.0+ |

---

## 废弃和移除的 API

截至 v2.66.8，没有发现明确标记为 deprecated 或移除的 API。所有变更都是向后兼容的添加或增强。

---

## 重要注意事项

### 1. 函数签名变更

**Cascader.showSearch.renderOption** 是唯一的函数签名变更，但它是向后兼容的（第三个参数是可选的）。

### 2. 类型导出变更

如果你的代码中显式引用了以下类型，需要注意导出路径可能变化：
- `MessageHookReturnType`
- `ModalHookReturnType`
- `NotificationHookReturnType`
- `TableInstance`

### 3. 新组件使用

使用新组件时需要确保版本兼容：
- ColorPicker: v2.47.0+
- VerificationCode: v2.54.0+
- Watermark: v2.61.0+

---

## 迁移检查清单

- [ ] 检查 Cascader 的 `showSearch.renderOption` 使用
- [ ] 检查 InputNumber 的 `onChange` 回调使用
- [ ] 检查 VirtualList 的 `onScroll` 回调使用
- [ ] 如果使用了 TypeScript，更新组件实例类型引用
- [ ] 如果使用了新 Props，确认最低版本要求
- [ ] 测试所有使用了变更组件的功能

---

**文档版本**: 1.0  
**更新日期**: 2026-01-09  
**适用版本**: Arco Design v2.51.2 → v2.66.8
