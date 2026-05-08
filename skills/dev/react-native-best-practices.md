---
name: react-native-best-practices
source: https://github.com/vercel-labs/agent-skills / callstackincubator
author: Callstack (Official)
category: dev
tags: [React Native, Expo, mobile, performance, iOS, Android]
collected: 2026-05-08
---

# react-native-best-practices

> React Native 和 Expo 开发最佳实践（16条规则，7个分类）。

## 触发场景

- 构建 React Native 或 Expo 应用
- 优化移动端性能
- 实现动画或手势
- 处理原生模块或平台特定 API

## 性能（Critical）

### 使用 FlashList 替代 FlatList

```tsx
// ❌ FlatList（对大数据集有性能问题）
import { FlatList } from 'react-native'
<FlatList data={items} renderItem={...} />

// ✅ FlashList（回收机制，性能大幅提升）
import { FlashList } from '@shopify/flash-list'
<FlashList
  data={items}
  renderItem={({ item }) => <Item item={item} />}
  estimatedItemSize={80}  // 关键：提供预估高度
/>
```

### 避免匿名函数和内联样式

```tsx
// ❌ 每次渲染创建新对象 → 触发子组件重渲染
<View style={{ padding: 16, margin: 8 }}>
  <Button onPress={() => handlePress(item.id)} />
</View>

// ✅ 提取到组件外部
const styles = StyleSheet.create({
  container: { padding: 16, margin: 8 }
})
const handlePress = useCallback((id) => doSomething(id), [])

<View style={styles.container}>
  <Button onPress={handlePress} />
</View>
```

### 繁重计算移到 Worklet

```tsx
import { runOnJS, useSharedValue, useDerivedValue } from 'react-native-reanimated'

// ✅ 复杂计算在 UI 线程运行，不阻塞 JS 线程
const derived = useDerivedValue(() => {
  'worklet'
  return Math.sqrt(sharedValue.value ** 2)
})
```

## 布局（High）

```tsx
// ✅ 安全区域处理（刘海/底部手势条）
import { SafeAreaView } from 'react-native-safe-area-context'
<SafeAreaView edges={['top', 'bottom']} style={{ flex: 1 }}>

// ✅ 键盘遮挡处理
import { KeyboardAvoidingView } from 'react-native'
<KeyboardAvoidingView behavior={Platform.OS === 'ios' ? 'padding' : 'height'}>
  <TextInput ... />
</KeyboardAvoidingView>

// ✅ Flex 布局（避免固定尺寸）
<View style={{ flex: 1 }}>           // 占满剩余空间
  <View style={{ flexShrink: 1 }}>   // 按需收缩
```

## 动画（High）

```tsx
// ✅ 使用 Reanimated 3（在 UI 线程运行，60fps）
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  withTiming
} from 'react-native-reanimated'

function AnimatedButton() {
  const scale = useSharedValue(1)

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }]
  }))

  return (
    <Animated.View style={animatedStyle}>
      <Pressable
        onPressIn={() => { scale.value = withSpring(0.95) }}
        onPressOut={() => { scale.value = withSpring(1) }}
      >
        <Text>Press me</Text>
      </Pressable>
    </Animated.View>
  )
}
```

## 图片（Medium）

```tsx
// ✅ 使用 expo-image（比内置 Image 更快，自动缓存）
import { Image } from 'expo-image'

<Image
  source="https://example.com/photo.jpg"
  style={{ width: 200, height: 200 }}
  placeholder={{ blurhash: 'L6Pj0^...' }}  // 模糊占位符
  contentFit="cover"
  transition={300}
/>
```

## 状态管理（Medium）

```tsx
// ✅ Zustand（轻量、不需要 Provider）
import { create } from 'zustand'

const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}))

// 组件中直接使用
const count = useStore((state) => state.count)
```

## 平台差异处理

```tsx
import { Platform } from 'react-native'

// 平台特定代码
const shadowStyle = Platform.select({
  ios: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.15,
    shadowRadius: 4,
  },
  android: {
    elevation: 4,
  },
})
```

## 快速配置（Expo）

```bash
npx create-expo-app my-app --template blank-typescript
cd my-app
npx expo install @shopify/flash-list expo-image react-native-reanimated react-native-gesture-handler zustand
```
