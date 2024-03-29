# vivus.js

- [線上 demo site (主要官網)](http://maxwellito.github.io/vivus)
- [Vivus Instant (線上 svg 動畫測試工具)](https://maxwellito.github.io/vivus-instant/)

## 開始使用一：Inline SVG

```HTML
<svg id="my-svg">
  <path></path>
  <path></path>
  <path></path>
</svg>
```
```JS
<script>
  new Vivus('my-svg', {duration: 200}, myCallback);
</script>
```

## 開始使用二：Dynamic load
#### 方法一
```HTML
<object id="my-svg" type="image/svg+xml" data="link/to/my.svg"></object>
```
```JS
<script>
  new Vivus('my-svg', { duration: 200 }, myCallback);
</script>
```

#### 方法二

```HTML
<div id="my-div"></div>
```
```JS
<script>
  new Vivus('my-div', { duration: 200, file: 'link/to/my.svg' }, myCallback);
</script>
```

## Option list

| Name               | Type     | Description                                                                                                                                               |
| ------------------ | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `type`               | string   | 動畫模式: `delayed`, `sync`, `oneByOne`, `script`, `scenario` or `scenario-sync`. [Default: delayed]                                                      |
| `file`               | string   | 鏈接 SVG 檔案進行動畫處理。如果設置，Vivus將創建一個對象標籤並將其附加到提供給構造函數的DOM元素中。注意，在播放Vivus實例之前，請使用 `onReady` callback。 |
| `start`              | string   | 定義何時觸發動畫。（`inViewport`：SVG進入inViewport後；`manual`：自由調用draw方法開始；`autostart`：立即開始）。[Default: inViewport]                     |
| `duration`           | integer  | 動畫持續時間，以 **frames** 為單位。 [Default: 200]                                                                                                       |
| `delay`              | integer  | 繪製第一個和最後一個路徑之間的時間，以 **frames** 為單位。 (只支援 `delayed` 動畫模式)                                                                    |
| `onReady`            | function | 當動畫準備好播放時，使用此函式。                                                                                                                          |
| `pathTimingFunction` | function | SVG 每個路徑元素的定時動畫功能。[(timing function)](https://github.com/maxwellito/vivus#timing-function)                                                  |
| `animTimingFunction` | function | 完整SVG的定時動畫功能。[(timing function)](https://github.com/maxwellito/vivus#timing-function)                                                           |
| `dashGap`            | integer  | dashes 之間的空白多餘邊距。如果在動畫的初始狀態出現毛刺，請增加該值。[Default: 2]                                                                         |
| `forceRender`        | boolean  | 強制瀏覽器重新呈現所有更新的路徑項。 默認情況下，該值僅在 IE 上為 `true`。（有關更多詳細信息，請參見“疑難解答”部分）。                                    |
| `reverseStack`       | boolean  | 顛倒執行順序。默認行為是從SVG中的第一個“路徑”渲染到最後一個路徑。此選項使您可以撤銷訂單。[Default: false]                                                 |
| `selfDestroy`        | boolean  | 刪除SVG上的所有多餘樣式，並將其保留為原始樣式。                                                                                                           |

## Methods

| Name                         | Description                                                                                  |
| ---------------------------- | -------------------------------------------------------------------------------------------- |
| `play(speed, callback)`      | 指定動畫的播放速度，負值為向後移動。0~1 之間為慢速移動；>1 為快速移動；<0 為在當前狀態倒退。 |
| `stop() `                    | 停止動畫。                                                                                   |
| `reset()`                    | 重新初始化 svg 至初始狀態：undrawn。                                                         |
| `finish() `                  | 重新初始化 svg 至最終狀態：drawn。                                                           |
| `setFrameProgress(progress)` | 設置動畫的進度，進度必須是0到1之間的數字。                                                   |
| `getStatus() `               | 獲取目前動畫的狀態：`start`，`progress`，`end`。                                             |
| `destroy() `                 | 重置 SVG (but make the instance out of order)。                                              |

這些 Methods return the object ，因此您可以鏈接操作，範例如下：

```JS
const myVivus = new Vivus('my-svg-id');
myVivus.stop().reset().play(2);
```

## Timing function

`EASE`, `EASE_IN`, `EASE_OUT` and `EASE_OUT_BOUNCE`

## Extra attributes

屬性 `data-ignore` 可以忽略 vivus 動畫中的路徑標籤。

```HTML
<svg id="my-svg">
  <path...>
  <path data-ignore="true" ...>
  <path...>
</svg>
```

## Scenarize (腳本)

- `data-start` (integer) 動畫開始的時間（以 **frames** 為單位）
- `data-duration` (integer) 該路徑的動畫持續時間（以 **frames** 為單位）

```HTML
<svg>
  <path data-start="0" data-duration="10" ... />
  <path data-start="20" data-duration="10" ... />
  <path data-start="20" data-duration="20" ... />
  <path data-start="0" data-duration="30" ... />
</svg>
```

## Scenarize (腳本同步)

- `data-delay` 前一條路徑的動畫結束與當前路徑的開始之間的時間（以 **frames** 為單位）
- `data-duration` (integer) 此路徑動畫的持續時間（以 **frames** 為單位）
- `data-async` (no value required) 使此路徑的繪製異步。意味著下一條路徑將同時開始。如果路徑沒有持續時間或延遲的屬性，則將使用選項中設置的默認值。

```HTML
<svg>
  <path data-duration="10" ... />
  <path data-delay="10" data-async ... />
  <path data-delay="15" ... />
  <path data-duration="10" data-delay="45" data-async ... />
  <path data-duration="50" data-delay="5" ... />
</svg>
```

## Debug

```JS
const logo = new Vivus('myLogo', { type: 'scenario-sync' });

// The property 'map' contain all the SVG mapping
console.table(logo.map);
```