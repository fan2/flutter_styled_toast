
## 代码结构分析

## StyledToast

```Dart
// lib/src/styled_toast.dart

///Current context of the page which uses the toast
BuildContext currentContext;

class StyledToast extends StatefulWidget
```

### 实现分析

```Dart
// lib/src/styled_toast.dart

class _StyledToastState extends State<StyledToast> {

  @override
  Widget build(BuildContext context) {
    Widget overlay = Overlay(
      initialEntries: <OverlayEntry>[
        OverlayEntry(builder: (ctx) {
          currentContext = ctx;
          return widget.child;
        })
      ],
    );

  }

}
```

在 _StyledToastState.build 中：

1. OverlayEntry 的 builder 返回 `widget.child`，然后封装为 Overlay` overlay`；  
2. 通过 Directionality 包裹 overlay 作为 child 构造 `Widget wrapper`；  
3. 传入上一步的 wrapper 构造返回 `StyledToastTheme`（继承自 InheritedWidget）。  

> 全局变量 `currentContext` 记录了 build 传参 context。

### 全局初始化

**StyledToast** 包裹 `MaterialApp` 作为其 child 参数，注意不是包裹 `Scaffold`！！！

> 这里初始化机制同 [oktoast](https://pub.dev/packages/oktoast) fork [github](https://github.com/fan2/flutter_oktoast)。

```Dart
// Simple global configuration, wrap you app with StyledToast.
// lib/src/styled_toast.dart

StyledToast(
  locale: const Locale('en', 'US'),
  child: MaterialApp(
            title: appTitle,
            showPerformanceOverlay: showPerformance,
            home: LayoutBuilder(
              builder: (BuildContext context, BoxConstraints constraints) {
                return MyHomePage(
                  title: appTitle,
                  onSetting: onSettingCallback,
                );
              },
            ),
          ),
);
```

> 全局变量 `currentContext` 记录了主 APP 的 BuildContext。

## 核心接口

- showToast  
- showToastWidget  

`showToast` 接口，没有设定的参数默认读取 StyledToastTheme；内部创建 *widget* 后传参调用 `showToastWidget`。

> 后续在一些子级页面中调用 *showToast* 时，也可传入界面的 `context` 作为当次弹toast的 BuildContext。

参考 [oktoast](https://pub.dev/packages/oktoast) fork [github](https://github.com/fan2/flutter_oktoast)，接口也是 showToast、showToastWidget，返回 ToastFuture 并加入 ToastManager 管理。

### showToastWidget

其中 **showToastWidget** 核心实现如下：

1. 构建 `OverlayEntry`：IgnorePointer-*_StyledToastWidget* 实现；  
2. 将 OverlayEntry 封装为` ToastFuture`，并插入到 OverlayState；  
3. 将 ToastFuture 添加到 `ToastManager` 并返回，可通过返回的 ToastFuture 关闭。  

> `_StyledToastWidget` 对应 oktoast 中的 `_ToastContainer`。

```Dart
// lib/src/styled_toast.dart

class _StyledToastWidget extends StatefulWidget
```

## 其他参考

[oktoast](https://pub.dev/packages/oktoast) fork [github](https://github.com/fan2/flutter_oktoast)
