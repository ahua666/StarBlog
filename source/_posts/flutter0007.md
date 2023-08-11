---
title: flutter强制刷新组件
date: 2023-08-02 23:10:48
categories: flutter
tags:
  - flutter
  - android
top_img: 'https://img.ahuaaa.cn/img/20230731171324.png'
cover: 'https://img.ahuaaa.cn/img/20230731171324.png'

---

## 前言
今天用了一个flutter的pdf插件，但是在更新pdf的时候，发现没有刷新，查看源码发现是用到了安卓原生,于是就想到了强制刷新组件。

## 使用Key强制刷新组件
使用 Key 属性：为 PDFView 组件提供一个唯一的 Key 属性。这样，当您需要更新数据时，可以通过更改 Key 属性来触发 PDFView 组件的重建。
```dart
class _PDFViewState extends State<PDFView> {
  Key _pdfViewKey = UniqueKey();

  @override
  Widget build(BuildContext context) {
    return PDFView(
      key: _pdfViewKey, // 设置唯一的 Key 属性
      pdfData: widget.pdfData,
      // 其他属性和回调函数...
    );
  }

  void updatePDFData(Uint8List newData) {
    setState(() {
      _pdfViewKey = UniqueKey(); // 更改 Key 属性以触发组件重建
      widget.pdfData = newData; // 更新 PDF 数据
    });
  }

  // 其他代码...
}
```
在 updatePDFData 方法中，通过调用 setState 来更新 PDFView 组件的数据，并更改 Key 属性。这将触发 PDFView 组件的重建，并显示新的数据。
请注意，这种方法可能会导致 PDFView 组件重新加载整个 PDF 文件，可能会带来性能上的开销。因此，根据具体需求和性能要求来决定是否使用此方法。
## UniqueKey
`UniqueKey()` 是 Flutter 中的一个函数，用于生成一个唯一的 `Key` 对象。`Key` 对象在 Flutter 中用于标识和区分不同的控件。通过为控件提供唯一的 `Key`，可以确保在控件树中正确地识别和更新相应的控件。

`UniqueKey()` 函数会生成一个新的 `Key` 对象，每次调用都会生成一个不同的对象。这意味着当您将 `UniqueKey()` 分配给控件的 `key` 属性时，Flutter 将视它们为不同的控件实例，即使其他属性相同，也会触发控件的重建。

在前面提到的示例中，我们使用 `UniqueKey()` 来生成一个唯一的 `Key` 对象，并将其分配给 `PDFView` 控件的 `key` 属性。当需要更新 `PDFView` 的数据时，我们通过更改 `Key` 对象来触发控件的重建和刷新。

请注意，`UniqueKey()` 生成的 `Key` 对象是不可变的，即一旦创建就不会更改。这确保了在控件树中的唯一性和稳定性。
