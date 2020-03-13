# Dark Mode

JSBox 最新版已支持 Dark Mode，不仅是应用本身，更提供了一套 API 让脚本也可以很好地支持 Dark Mode。

本章我们将会讲解 Dark Mode 的机制是什么，以及如何对脚本进行适配。

# theme

`theme` 指定了该脚本的外观偏好，可选值是 `light` / `dark` / `auto`，分别表示始终 Light、始终 Dark，以及自动模式。

可以为脚本全局指定这个参数，例如：

```js
$app.theme = "auto";
```

或是在 [config.json](package/intro.md) 文件中指定：

```json
{
  "settings": {
    "theme": "auto"
  }
}
```

除了全局设置，还可以为某个单独的页面指定不同的 `theme`，写在其 `props` 里面即可：

```js
$ui.push({
  props: {
    "theme": "light"
  }
});
```

> 请注意，为了避免对现有的脚本造成破坏性的改动，目前脚本默认的 `theme` 是 `light`，也既浅色模式。如果需要适配 Dark Mode，请将其设置为 `auto` 后，再进行颜色的适配工作。

在不同的 theme 下，控件的默认颜色会有所不同，请参考更新后的 [UIKit-Catalog](https://github.com/cyanzhong/xTeko/blob/master/extension-scripts/uikit-catalog.js) 样例以了解更多。

# 动态颜色

为了更好地为 Light 和 Dark 适配不同的颜色，现提供动态颜色接口，例如：

```js
const dynamicColor = $color({
  light: "#FFFFFF",
  dark: "#000000"
});
```

也可以简写为：

```js
const dynamicColor = $color("#FFFFFF", "#000000");
```

写法支持嵌套，你可以用 `$rgba(...)` 接口生成颜色后，用 `$color(...)` 接口生成动态颜色：

```js
const dynamicColor = $color($rgba(0, 0, 0, 1), $rgba(255, 255, 255, 1));
```

动态颜色会在 Light 和 Dark 模式下分别呈现不同的颜色，而无需通过监听 Dark Mode 变化之后进行重新设置。

同时，`$color(...)` 接口也支持了几个[语义化颜色](function/index.md?id=colorstring)，让你可以更方便地调用。

# events: themeChanged

对于任何一个控件，都能收到一个 Dark Mode 改变时的事件，例如：

```js
$ui.render({
  views: [
    {
      type: "view",
      layout: $layout.fill,
      events: {
        themeChanged: (sender, isDarkMode) => {
          // Update UI if needed
        }
      }
    }
  ]
});
```

这提供了需要动态改变一些元素的机会，例如根据是否 Dark Mode 来决定用哪张图片，或者改变控件的 alpha 值。

# 如何适配 Dark Mode

大体上来说，让脚本支持 Dark Mode 有三个步骤：

- 将 `theme` 设置为 `auto`
- 使用 `$color(light, dark)` 提供动态颜色
- 使用 `themeChanged` 来完善一些需要动态改变的元素

随着机制不断完善，之后可能会增加动态图片接口，让适配工作变得更简单。同时，`theme` 默认为 `light` 是过渡期的一个设置，之后可能会改为默认为 `auto`，方便仅使用默认控件的脚本直接完美支持 Dark Mode。