[ ![Download](https://api.bintray.com/packages/arasthel/maven/spannedgridlayoutmanager/images/download.svg) ](https://bintray.com/arasthel/maven/spannedgridlayoutmanager/_latestVersion) [![API](https://img.shields.io/badge/API-14%2B-brightgreen.svg?style=flat)](https://android-arsenal.com/api?level=14) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

# SpannedGridLayoutManager

**SpannedGridLayoutManager** is a `RecyclerView.LayoutManager` that resizes and reorders views based on a provided `SpanSize`.

> Important: this layout manager tries to fill empty gaps whenever possible. As a result, the visual order of items may differ from their adapter order. For example, `View #9` may be placed before `View #8` when it can fill an earlier empty area.

![video](art/spannedgridlayout.gif)

## 中文说明

这是一个用于 Android `RecyclerView` 的跨格布局管理器，适合实现桌面卡片、仪表盘、宫格面板等场景。

主要特性：

- 支持横向和纵向滚动。
- 支持不同尺寸的卡片，例如 `1 x 1`、`2 x 1`、`2 x 2`。
- 通过 `SpanSizeLookup` 为不同位置动态指定跨度。
- 自动填补空白区域，减少布局空洞。
- 支持跨度缓存，适合复杂尺寸计算场景。

适用场景：

- Android 车机桌面卡片布局。
- 仪表盘、控制面板、快捷入口区域。
- 固定网格中的多尺寸卡片展示。

## Usage

### Gradle dependency

```groovy
dependencies {
    implementation 'com.arasthel:spannedgridlayoutmanager:3.0.2'
}
```

### Create the layout manager

When you create a new `SpannedGridLayoutManager`, provide:

- An `Orientation`: `Orientation.VERTICAL` or `Orientation.HORIZONTAL`.
- The span count used to divide each row or column.

**Kotlin**

```kotlin
val layoutManager = SpannedGridLayoutManager(
    orientation = SpannedGridLayoutManager.Orientation.VERTICAL,
    spans = 4
)
recyclerView.layoutManager = layoutManager
```

**Java**

```java
SpannedGridLayoutManager layoutManager = new SpannedGridLayoutManager(
        SpannedGridLayoutManager.Orientation.VERTICAL,
        4
);
recyclerView.setLayoutManager(layoutManager);
```

### Set card sizes

Use `SpannedGridLayoutManager.SpanSizeLookup` to return a `SpanSize` for each adapter position.

**Kotlin**

```kotlin
layoutManager.spanSizeLookup = SpannedGridLayoutManager.SpanSizeLookup { position ->
    when (position) {
        0 -> SpanSize(2, 2)
        1 -> SpanSize(2, 1)
        else -> SpanSize(1, 1)
    }
}
```

Enable caching when span calculation is expensive:

```kotlin
layoutManager.spanSizeLookup?.usesCache = true
layoutManager.spanSizeLookup?.invalidateCache()
```

### Stable item order

Scroll restoration is disabled by default when item sizes change significantly. Enable it only when item order remains stable:

```kotlin
layoutManager.itemOrderIsStable = true
```

## Animations

To enable layout animations similar to the sample:

- Call `setHasStableIds(true)` in the adapter.
- Override `getItemId()` and return a stable ID.
- Call `adapter.notifyDataSetChanged()` to trigger relayout.

## Implementation details

This library uses free-space `Rect` areas to locate empty gaps and choose where each item should be placed. The algorithm is described in [free_space_algorithm.pdf](free_space_algorithm.pdf).

The core implementation is located at:

```text
spannedgridlayoutmanager/src/main/java/com/arasthel/spannedgridlayoutmanager/
```

## Notes

- The repository uses the legacy Android support library namespace. Migrating to AndroidX may be necessary for newer Android projects.
- Automatic gap filling can change visual order. Do not use this behavior when strict adapter-order rendering is required.
- For very large data sets, benchmark relayout performance before using it in production.

## License

MIT License.

Copyright © 2017 Jorge Martín Espinosa
