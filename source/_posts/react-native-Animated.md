---
title: react-native Animated
date: 2020-04-15 21:15:46
tags: react-native Animated
---

## react native animated
最近在项目中需要实现一个 [ScrollView][2] 的吸顶效果;
最开始的时候打算直接用 [ScrollView][2] 的 onScroll 方法动态设置 吸顶的高度;
``` javascript 
 const [offset, $offset] = useState(0);
 ...
 <ScrollView
 onScroll={e=>$offset(e.nativeEvent.contentOffset.y)}
 scrollEventThrottle={1}
 >
 <View
 style={[style.headerStyle,{
     transform:[{
         translateY:offset
     }]
 }]}
 >
 ...

```
但是效果并不理想,受控的View 有明显的抖动现象,然后在网上找资料发现需要用 react native [^Animated] 动画来实现吸顶效果.

于是就有了以下代码:

``` javascript 
 const offset = useRef(Animated.Value(0)).current;

 const $offset =(target)=> Animated.add(offset,{
     toValue:target,
     duration:1
 }).start()
  <ScrollView
 onScroll={e=>$offset(e.nativeEvent.contentOffset.y)}
 scrollEventThrottle={1}
 >
 <Animated.View
 style={[style.headerStyle,{
     transform:[{
         translateY:offset
     }]
 }]}
 >

```
然后,效果和一开始一样,并没有什么用.查阅api 文档之后发现 [Animated][1] 有一个 useNativeDriver 参数;
然后在网上找到了一份[demo][3].
下面是实现流畅吸顶的关键代码.
``` javaScript
 const offset = useRef(Animated.Value(0)).current;
 <Animated.ScrollView
 onScroll={
    Animated.event(
      [{
        nativeEvent: { contentOffset: { y: offset } } // 记录滑动距离
      }],
      { useNativeDriver: true }) // 使用原生动画驱动
  }
  scrollEventThrottle={1}>

```
``` javaScript 
const translateY = offset.interpolate({
  inputRange: [-1, 0, 0, 1],
  outputRange: [0, 0, 0, 1],
});
...

<Animated.View
style = {[style, styles.container, { transform: [{ translateY }] }]}
> 
```



记录备用;
资料传送门:

 [3]: https://www.jb51.net/article/162381.htm
 [1]: https://reactnative.dev/docs/animated
 [2]: https://reactnative.dev/docs/scrollview


