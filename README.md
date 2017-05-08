# FullPlugin
全量插件化，用于中小App免发布升级

# 介绍

1. 大部分中小型App没有必要拆分成若干插件，那么可以将整个APK作为一个插件，单独写一个宿主壳，从而实现APK的动态更新，取了一个名字叫全量插件化。

2. 对于超级App，还是建议讲App拆成若果插件，使用宿主+ 若干插件的方式来使用插件化。

# 实现
基于DroidPlugin制作宿主，正常开发后的App作为插件，宿主的作用是1、加载插件 2、升级插件 3、插件权限控制。

1. 加载插件
第一次安装时将存储在Assets里的Apk 拷贝到内存，安装插件。

2. 升级插件
根据应用服务器返回的App版本信息下载新Apk，然后进行签名验证，进行版本控制替换内存Apk。下次启动新Apk。

3. 宿主申请插件中用的权限。

# 注意

1. DroidPlugin默认不支持多开的，即插件和宿主的packagename是不能一样的。但此项目的需求是对插件App做到无侵入性，所以需要对DroidPlugin改造使其支持多开。修改后的DroidPlugin地址：https://github.com/MasonLiuChn/DroidPlugin

2. 目前实现DroidPlugin多开的方法是在宿主加载插件时改变插件的packagename，所以插件代码中若使用context.getPackageName时是获取宿主改变后的name。所以在插件中可以使用以下代码替换
```java
public class FullPluginUtil {
    private static String packageName;

    public static String getPackageName() {
        if (packageName == null) {
            try {
                Class<?> activityThread = Class.forName("android.app.ActivityThread");
                Method currentPackage = activityThread.getMethod("currentPackageName");
                packageName = (String) currentPackage.invoke(null, (Object[]) null);
            } catch (Throwable t) {
                t.printStackTrace();
                packageName = "";
            }
        }
        return packageName;
    }

    public static boolean runningAsPlugin(Context context) {
        if (context.getPackageName().endsWith(".plugin")) {
            return true;
        }
        return false;
    }
}
```
3代码比较简单且还在踩坑且不方便开源

# Contact me:

- Blog:http://www.masonliu.com

- Email:MasonLiuChn@gmail.com
