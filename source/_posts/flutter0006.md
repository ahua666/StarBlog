---
title: flutter OutlinedButton按钮分析
top_img: 'https://img.ahuaaa.cn/img/20230731171324.png'
cover: 'https://img.ahuaaa.cn/img/20230731171324.png'
categories: flutter
tags:
  - flutter
  - android
abbrlink: 24297
date: 2023-07-22 02:19:23
---

### 资料参考

[Migrating to the New Material Buttons and their Themes
](https://docs.google.com/document/d/1yohSuYrvyya5V1hB6j9pJskavCdVq9sVeTqSoEPsWH0/edit)

### `OutlinedButton` 类

`OutlinedButton` 类是一个 Material Design 风格的 "Outlined Button"，它继承自 `ButtonStyleButton` 类，表示一个带有按钮样式的可点击组件。

`OutlinedButton` 类的构造函数如下：

```dart
const OutlinedButton({
  super.key,
  required super.onPressed,
  super.onLongPress,
  super.onHover,
  super.onFocusChange,
  super.style,
  super.focusNode,
  super.autofocus = false,
  super.clipBehavior = Clip.none,
  super.statesController,
  required super.child,
});
```

这个构造函数接受多个参数，其中一些是从父类 `ButtonStyleButton`
继承的。这些参数包括 `key`、`onPressed`、`onLongPress`、`onHover`、`onFocusChange`、`style`、`focusNode`、`autofocus`、`clipBehavior`、`statesController`
和 `child`。这些参数用于配置按钮的行为和外观。

### `OutlinedButton.icon` 工厂构造函数

`OutlinedButton.icon` 是一个工厂构造函数，用于创建一个带有图标和标签的 `OutlinedButton` 实例。它的定义如下：

```dart
factory OutlinedButton.icon({
  Key? key,
  required VoidCallback? onPressed,
  VoidCallback? onLongPress,
  ButtonStyle? style,
  FocusNode? focusNode,
  bool? autofocus,
  Clip? clipBehavior,
  MaterialStatesController? statesController,
  required Widget icon,
  required Widget label,
}) = _OutlinedButtonWithIcon;
```

这个构造函数接受多个参数，包括 `key`、`onPressed`、`onLongPress`、`style`、`focusNode`、`autofocus`、`clipBehavior`、`statesController`、`icon`
和 `label`。其中，`icon` 和 `label` 是必需的参数，用于指定按钮中显示的图标和标签。

`OutlinedButton.icon` 构造函数创建了一个 `_OutlinedButtonWithIcon` 实例，它是 `OutlinedButton` 的一个子类，它通过传递参数来配置按钮的行为和外观。
好的，我们继续分析剩下的部分。

### `styleFrom` 静态方法

`styleFrom` 是一个静态方法，用于根据简单值创建一个 `ButtonStyle` 实例。它的定义如下：

- `foregroundColor`：设置按钮的前景色，即文本和图标的颜色。
- `backgroundColor`：设置按钮的背景色。
- `disabledForegroundColor`：设置按钮在禁用状态下的前景色。
- `disabledBackgroundColor`：设置按钮在禁用状态下的背景色。
- `textStyle`：设置按钮中文本的样式，如字体大小、字体粗细等。
- `padding`：设置按钮的内边距。
- `minimumSize`：设置按钮的最小尺寸。
- `side`：设置按钮的边框样式。
- `shape`：设置按钮的形状，如圆角矩形、圆形等。
- `alignment`：设置按钮内容的对齐方式。
- `visualDensity`：设置按钮的视觉密度。
- `tapTargetSize`：设置按钮的点击目标大小。
- `overlayColor`：设置按钮的叠加颜色，用于点击时的水波纹效果。
- `shadowColor`：设置按钮的阴影颜色。
- `elevation`：设置按钮的海拔高度，影响阴影效果。
- `mouseCursor`：设置鼠标悬停在按钮上时的光标样式。
- `visualShape`：设置按钮的可视形状。
- `animationDuration`：设置按钮状态变化的动画持续时间。
- `enableFeedback`：设置按钮是否启用触觉反馈。
- `tapBehavior`：设置按钮的点击行为，如不可点击、禁用点击等。

以上是 `styleFrom` 方法中的全部参数及其作用。可以根据需要选择其中的一个或多个参数，并传递相应的值来定制按钮的外观和行为。

### `defaultStyleOf` 方法

`defaultStyleOf` 是 `OutlinedButton` 类中的一个静态方法，用于获取默认的按钮样式。它的定义如下：

```dart
static ButtonStyle defaultStyleOf(BuildContext context) {
  final ThemeData theme = Theme.of(context);
  final ColorScheme colorScheme = theme.colorScheme;
  final ButtonStyle style = ElevatedButton.styleFrom(
    primary: colorScheme.primary,
    onPrimary: colorScheme.onPrimary,
    onSurface: colorScheme.onSurface,
    textStyle: theme.textTheme.button,
  ).copyWith(
    side: MaterialStateProperty.all<BorderSide>(
      BorderSide(
        color: colorScheme.primary,
        width: 1,
        style: BorderStyle.solid,
      ),
    ),
  );
  return style;
}
```

这个方法接受一个 `BuildContext` 对象作为参数，用于获取当前主题的颜色和文本样式。

在方法体中，首先通过 `Theme.of(context)` 获取当前的主题数据。然后，使用主题数据中的颜色方案和文本样式来设置按钮的默认样式。

具体来说，`defaultStyleOf` 方法使用 `ElevatedButton.styleFrom` 方法创建一个基本的按钮样式，然后通过 `copyWith`
方法进行进一步的定制。以下是对一些关键属性的解释：

- `primary`：设置按钮的主要颜色，通常是按钮的背景色。
- `onPrimary`：设置按钮上文本和图标的颜色。
- `onSurface`：设置按钮在表面（即非禁用状态）上文本和图标的颜色。
- `textStyle`：设置按钮中文本的样式，如字体大小、字体粗细等。
- `side`：设置按钮的边框样式。

最后，`defaultStyleOf` 方法返回一个包含默认样式的 `ButtonStyle` 实例。

可以在创建按钮时使用 `defaultStyleOf` 方法来获取默认样式，并根据需要进行进一步的定制。

### `_scaledPadding` 函数

`_scaledPadding` 是 `OutlinedButton` 类中的一个私有函数，用于计算按钮的内边距。它的定义如下：

```dart
EdgeInsetsGeometry _scaledPadding(
  EdgeInsetsGeometry padding,
  VisualDensity visualDensity,
) {
  if (padding.isNonUniform) {
    return visualDensity.baseSizeAdjustment * padding;
  }
  final double delta = visualDensity.baseSizeAdjustment;
  return EdgeInsets.all(delta) + padding;
}
```

这个函数接受两个参数：`padding` 和 `visualDensity`。`padding` 是按钮的原始内边距，`visualDensity` 是按钮的视觉密度。

在函数体中，首先判断 `padding`
是否是非均匀的内边距，即上、下、左、右四个方向的内边距是否不相等。如果是非均匀的内边距，那么乘以 `visualDensity.baseSizeAdjustment`
来调整内边距的大小。

如果 `padding` 是均匀的内边距，那么将 `visualDensity.baseSizeAdjustment` 的值加到每个方向的内边距上。

最后，返回计算后的 `EdgeInsetsGeometry` 对象，表示调整后的内边距。

`_scaledPadding` 函数的作用是根据按钮的视觉密度调整按钮的内边距。视觉密度可以影响按钮的大小和间距，以适应不同的屏幕尺寸和设备像素密度。

这个函数通常在按钮的布局过程中被调用，用于计算最终的内边距值，以确保按钮在不同的视觉密度下保持一致的外观。