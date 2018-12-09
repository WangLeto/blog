---

title: 解决 TextView 中 drawableleft 图片大小不可控问题

date: 2018-04-25 07:46:50

tags: Android

---

这个问题在 Stack Overflow 上有过解答了，解决方案还是很不错的（前提是使用的 **drawable 资源是矢量化**的，对于位图可能无效）。

在资源文件的 `android:drawble` 属性中写入 `dimen` 中的量：
<!--more-->

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >

  <item
      android:drawable="@drawable/icon"
      android:width="@dimen/icon_size"
      android:height="@dimen/icon_size"
      />

</layer-list >
```

但是注意，这种方法并不兼容 API21 以下，但也有解决方法，即在 `build.gradle` 文件中添加以下依赖：

```json
// Gradle Plugin 2.0+
android {
  defaultConfig {
    vectorDrawables.useSupportLibrary = true
  }
}
```



问题源自 [how to change a drawableLeft icon size on a button?](https://stackoverflow.com/questions/40998914/how-to-change-a-drawableleft-icon-size-on-a-button) 与 [How to use vector drawables in Android API lower 21?](https://stackoverflow.com/questions/34417843/how-to-use-vector-drawables-in-android-api-lower-21)。亲自参与了第一个问题的高票答案编辑，希望答主接受啊（啊啊我的 Stack Overflow 账号只有 30 贡献，要不直接添加评论了啊）……
