---
title: React Navigation 4 完全指南
---

> 本文适用于 React Native 0.60 及更高版本

## 优势

- 易于使用：用内置的导航器快速开始项目, 提供开箱即用的无缝体验。
- 兼容 ios 和 android：平台拥有独特的外观、流程的动画和手势。
- 完全可定制：如果你知道如何使用 JavaScript 编写应用程序，那么你就可以自定义 React Navigation 的任何部分。
- 平台可扩展：React Navigation 在每一层都是可扩展的-你可以编写自己的 navigators，甚至替换面向用户的 API。

<!--more-->

## 安装

```sh
$ yarn add react-navigation
$ yarn add react-native-gesture-handler react-native-reanimated react-native-screens react-native-safe-area-context
```

## 链接

```sh
$ cd ios && pod install && cd ..
```

为了完成 react-native-screens 在 Android 上的安装, 请在`android/app/build.gradle`中 dependencies 选项中添加下面这两行:

```groovy
implementation 'androidx.appcompat:appcompat:1.1.0-rc01'
implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.1.0-alpha02'
```

## createAppContainer

createAppContainer 是一个函数，该函数返回一个 React 组件 并将 createStackNavigator 创建的 React 组件 作为参数，并且可以直接从 App.js 导出,用作我们应用程序的根组件。

### onNavigationStateChange(prevState, newState, action)

每当导航器管理的 navigation state 发生变化时，都会调用该函数。 它接收之前的 state、navigation 的新 state 以及发布状态更改的 action。 默认情况下，它将 state 的更改打印到控制台。我们可以利用该 props 进行页面跟踪并将其发送到 APM 系统。

```tsx
<AppContainer onNavigationStateChange={onNavigationStateChange} />
```

```ts
import { NavigationState, NavigationLeafRoute, NavigationParams, NavigationRoute } from 'react-navigation'

/**
 *  从 navigation state 中获取当前页面名
 * @param navigationState NavigationState
 */
const getCurrentRoute = (
  navigationState: NavigationState | NavigationLeafRoute<NavigationParams>,
): NavigationRoute<NavigationParams> | null => {
  if (!navigationState) {
    return null
  }
  const route = navigationState.routes[navigationState.index]
  // 在嵌套的导航中快速翻找
  if (route.routes) {
    return getCurrentRoute(route)
  }
  return route
}

const onNavigationStateChange = (prevState, currentState) => {
  const prevScreen = getCurrentRoute(prevState)
  const currentScreen = getCurrentRoute(currentState)
  if (currentScreen && prevScreen !== currentScreen) {
    console.log(currentScreen)
  }
}

export default onNavigationStateChange
```