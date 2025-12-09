多系统 API 数据结构不一致问题：

- 梳理全链路业务，提取字段核心语义，定义标准化字段
* 根据系统标识配置源字段 - 标准字段的映射表，封装格式转换函数
* 适配器接收系统标识和原始数据，遍历原始数据字段，根据系统标识和映射表查找对应标准字段，调用格式转换函数将源数据转换为标准数据，校验字段缺失和类型异常并以默认值为兜底
* 跨系统复用映射表、格式转换函数和适配器，通过「系统标识 + 原始数据哈希」为键缓存标准数据，长缓存静态数据，按需缓存动态数据

设计字段频繁增删修改的组件：

- 通过 JSON 全局配置字段模板，统一收敛字段属性如渲染类型、文案、字段类型、默认值、联动规则和校验规则等，组件只解析模板，不硬编码字段
- 将组件拆分静态的页面布局/业务交互逻辑与动态的字段渲染逻辑，构建通用字段渲染引擎，接收页面标识 + 字段配置，根据渲染类型映射至原子组件（输入框/表格单元/文本标签等），增删字段只需修改配置模板，组件代码零成本改动
* 性能优化：渲染隔离 + 状态隔离 + 非受控设计
* 自定义扩展：多语言联动 + 多草稿编辑

批量提交表单：

- 合并多表单数据，一次请求提交
- 多请求并发提交

判断表单组件为受控组件 & 非受控组件：

- 判断标准：React 组件状态控制 → 受控组件 + DOM 元素原生控制 → 非受控组件
- 底层原理：受控组件为 React 主动管理状态 → 驱动视图更新，非受控组件为 DOM 自主管理状态 → React 被动读取
- 表象特征：受控组件显式绑定  `value`  且通过  `setState`  触发更新，非受控组件通过  `defaultValue`  初始化且通过  `ref`  读取
- 设计取舍：受控组件适合复杂表单，非受控组件适合简单表单

修改表单对应字段只渲染部分组件：

- 渲染隔离：`React.memo/useMemo/useCallback` 依赖数组包含单一字段
- 状态隔离：将 `useState` 从 `<Form>` 根组件移至各个独立的 `<Field>` 组件内，各个 `<Field>` 组件管理自身的 `value` 状态，修改字段更新对应 `<Field>` 组件，不影响 `<Form>` 根组件
- 非受控设计：表单转换为非受控组件，各个字段通过 `ref` 访问 DOM 元素值，通过 `register`  注册后为独立字段，修改字段仅通知订阅该字段的组件重新渲染

表单联动：表单支持嵌套、实时校验、自动补全、多语言联动和多草稿编辑

1. 表单嵌套：通过"带层级标识的递归对象数组"描述树形嵌套结构，各个节点包含自身字段类型、默认值和子节点列表，适配商品 → SKU → 属性项的层级，将字段类型与对应 UI 组件相关联，若节点类型为数组或对象，渲染对应组件并递归渲染子节点列表，否则根据字段类型从组件映射表中查找对应的 UI 组件，传入父组件 props，通过增删按钮动态增删节点
2. 实时校验：各个字段配置校验规则并存储校验结果，支持同步/异步实时更新，内联逻辑 -> Schema -> 业务规则引擎

- 字段值联动：源字段值变化 → 目标字段值同步/异步更新
- 显隐/启禁联动：源字段值满足特定条件 → 目标字段显示/隐藏或启用/禁用
- 校验规则联动：源字段值变化 → 目标字段的校验规则动态切换
- 跨表单联动：表单 A 的字段变化 → 表单 B 的字段同步/异步更新
- Schema：将整个表单字段、样式、联动和校验规则转换为声明式的 JSON 结构，表单渲染引擎在运行时读取 Schema 并动态生成对应 UI、事件监听器和校验器
- 业务规则引擎：由包含 `CONDITION` 和 `ACTION` 的规则构成，当表单变化时，业务规则引擎根据其状态对象，判断所有规则的 `CONDITION`，收集条件为真的规则对应 `ACTION`，表单 UI 层接收 `ACTION` 集合后执行相应业务逻辑

3. 自动补全：根据父字段值映射筛选补全选项，选择补全选项后回显当前字段值并补全相关字段，通过 `Map` 和分级存储，键为字段值，值为补全结果

4. 多语言联动：存储多语言 JSON 文件
5. 多草稿编辑：通过全局 Store 分别独立存储各个草稿状态，为其分配唯一 ID 标识 `draftId` 并内嵌于页面 URL

选择器组件：

| classNames               | 配置选择器内置模块的 className                                                               | [Record<SemanticDOM, string>](https://ant.design/components/select-cn#semantic-dom)        | -                                                                           |
| ------------------------ | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| styles                   | 内联样式                                                                               | [Record<SemanticDOM, CSSProperties>](https://ant.design/components/select-cn#semantic-dom) | -                                                                           |
| defaultValue             | 指定默认选中的条目                                                                          | string \| string[] \|  <br>number \| number[] \|  <br>LabeledValue \| LabeledValue[]       | -                                                                           |
| fieldNames               | 自定义节点 label、value、options、groupLabel 字段                                            | object                                                                                     | { label: `label`, value: `value`, options: `options`, groupLabel: `label` } |
| filterOption             | 根据输入项筛选，当其为函数时，接收 `inputValue` 和 `option` 参数，当 `option` 符合筛选条件时，返回 true，反之返回 false | boolean \| function(inputValue, option)                                                    | true                                                                        |
| filterSort               | 筛选结果的排序函数                                                                          | (optionA: Option, optionB: Option, info: { searchValue: string }) => number                | -                                                                           |
| searchValue              | 搜索文本                                                                               | string                                                                                     | -                                                                           |
| placeholder              | 选择框默认文本                                                                            | string                                                                                     | -                                                                           |
| loading                  | 加载中状态                                                                              | boolean                                                                                    | false                                                                       |
| defaultOpen              | 是否默认展开                                                                             | boolean                                                                                    | -                                                                           |
| defaultActiveFirstOption | 是否默认高亮首个选项                                                                         | boolean                                                                                    | true                                                                        |
| maxCount/maxTagCount     | 选中项的最多数量                                                                           | number                                                                                     | -                                                                           |
| autoFocus                | 自动聚焦                                                                               | boolean                                                                                    | false                                                                       |
| allowClear               | 自定义清除按钮                                                                            | boolean \| { clearIcon?: ReactNode }                                                       | false                                                                       |
| disabled                 | 是否禁用                                                                               | boolean                                                                                    | false                                                                       |
| onSearch                 | 搜索框值变化时触发的回调                                                                       | function(value: string)                                                                    | -                                                                           |
| onChange                 | 选中项或输入值变化时触发的回调                                                                    | function(value, option:Option \| Array<Option>)                                            | -                                                                           |
| onSelect                 | 选中时触发的回调                                                                           | function(value: string \| number \| LabeledValue, option: Option)                          | -                                                                           |
| onDeSelect               | 取消选中时触发的回调                                                                         | function(value: string \| number \| LabeledValue)                                          | -                                                                           |
| onOpenChange             | 展开下拉菜单的回调                                                                          | (open: boolean) => void                                                                    | -                                                                           |
| onPopupScroll            | 下拉列表滚动时的回调                                                                         | (event: UIEvent) => void                                                                   | -                                                                           |
| onInputKeyDown           | 按键按下时回调                                                                            | (event: KeyboardEvent) => void                                                             | -                                                                           |
| onFocus                  | 获得焦点时触发的回调                                                                         | (event: FocusEvent) => void                                                                | -                                                                           |
| onBlur                   | 失去焦点时触发的回调                                                                         | function                                                                                   | -                                                                           |
| onClear                  | 清空内容时触发的回调                                                                         | function                                                                                   | -                                                                           |

通过 `div/button/ul/li` 等模拟原生选择器的触发区、下拉面板和选项列表，通过 React 状态控制选择器的加载/搜索状态、下拉面板的展开/收起和选项的单选/多选/高亮，结合事件监听和键盘导航还原交互逻辑

表格添加自定义筛选功能：

* 抽象接口：设置、获取和重置筛选条件及回调 + 合法性校验
* 筛选器：基础型筛选器（单选框/输入框/下拉框），单一条件筛选；复合型筛选器（复选框/区间选择），多维条件筛选；自定义筛选器，自定义条件筛选
* 管理生命周期：根据行列配置渲染表格，实例化对应自定义筛选器，回显保存的筛选条件，用户交互后筛选器执行业务逻辑，通过回调传递结构化参数给适配层，适配层将其转换为可识别格式，触发客户端或服务端筛选，根据过滤后的数据重新渲染表格并同步筛选器状态