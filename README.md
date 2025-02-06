# Pinepatch：基于 Pine 的免 Root Hook 框架

## 使用方法

1. 使用 Pinepatch 打开 APK 文件，例如：
   - 在 MT 管理器中点击 APK 文件
   - 长按 **查看** 按钮
   - 点击 **Pinepatch**
   - 即可开始修补 APK

2. 修补后的 APK **没有签名**，需要使用 MT 管理器、NP 管理器或其他工具进行签名。

3. 签名后即可安装，安装后可以通过模块修改软件的行为。

4. **安装模块** 的方法同样是使用 Pinepatch 打开文件。

## 模块仓库

[模块仓库](http://dotcog.ct.ws/pipatch_modules.html)

## 模块开发文档

### 1. 创建 `manifest.json`

```json
{
    "package": "$模块包名$",
    "version": "$模块版本$",
    "label": "$模块名称$",
    "description": "$模块介绍$"
}
```

### 2. 创建 Android 项目并导入 `pine_api.jar`

新建文件 `MainHook.java`，放在 Java 工程的根目录下：

```java
public class MainHook {
    public static void hook(ClassLoader classLoader, ApplicationInfo appInfo, Context context) throws Throwable {
        // 这里填写 Hook 代码
    }
}
```

### 3. 示例：让每个 `Activity` 启动时弹出 Toast

```java
import top.canyie.pine.*;
import top.canyie.pine.callback.*;
import android.app.*;
import android.widget.*;

public class MainHook {
    public static void hook(ClassLoader classLoader, ApplicationInfo appInfo, Context context) throws Throwable {
        Pine.hook(Pine.findMethod(Activity.class, "onCreate", Bundle.class),
            new MethodHook() {
                @Override
                public void afterCall(Pine.CallFrame frame) {
                    Toast.makeText((Activity) frame.thisObject, "My Toast", Toast.LENGTH_SHORT).show();
                }
            }
        );
    }
}
```

### 4. 编译模块

- 编译后，将生成的 DEX 文件重命名为 `dex`。
- 如果有多个 DEX 文件，需**合并**，并删除 DEX 中多余的类（如 `pine_api` 的类），否则 `pine_api` 会导致模块不生效。
- 最后，将 `dex` 和 `manifest.json` **压缩** 在一起，得到模块安装包。

## Pinepatch API 说明

Pinepatch 提供的 API 与原版 Pine **略有不同**，请自行查看源码。

此外，Pinepatch 模块还提供了专门访问 `ContentResolver.query` 的 API：

```java
Pine.queryContent(context, "com.exampleapp/example_query");
```

> **注意**：此处**无需**加前缀 `content://`。

## Pinepatch 管理模块功能

在 Pinepatch 管理器中，点击模块会自动跳转到**包名与模块包名相同的应用**的 `PiActivity` 界面。

例如：
- 一个模块的包名是 `nea.dpi`。
- 在管理器中点击该模块时，会自动跳转到 **包名 `nea.dpi`** 的应用的 **`nea.dpi.PiActivity`** 界面。

## 模块开发实例

[GitHub 示例项目](https://github.com/dotcog/dpi_setting)
