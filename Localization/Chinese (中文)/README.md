# Ampiri Unity 插件集成指南

目录
===

* [入门](#入门)
* [集合横幅广告](#集合横幅广告)
* [集合插屏广告](#集合插屏广告)
* [集合视频广告](#集合视频广告)
* [集合第三方SDKs](#集合第三方SDKs)
* [平台特定步骤](#平台特定步骤)

入门
===
1. 在 Unity editor 中打您的项目，并选择__Assets > Import Package > Custom Package__。找下载的_AmpiriPlugin.unitypackage_文件。
2. 确保所有的文件都被选中，并点击__Import__。
3. 在__Assets > Ampiri > Prefabs__, 把Ampiri.prefabs 拖入scene里面。
4. Android/iOS Ad Unit IDs 必须在出现的字段中输入。Ad Unit IDs可以从Ampiri管理页面得到。


要查看示例，请导入AmpiriUnityDemo.package。在那里，你应该看到__DemoApp__ scene 在scenes文件夹里和__Scripts__文件夹内是Ampiri使用脚本的一些示例

初始化
=====
您应该自能在app lifecycle中调用这个**一次**：
```
ampiriSDK.sdk.Init("https://api.ampiri.com", true);
```

集合横幅广告
===================


横幅广告只能在应用程序的屏幕顶部或下方显示。
要显示横幅：

可用的横幅广告尺寸
* 320x50 (recommended)
* 728x90

可用排列
* Center
* Left
* Right

自动更新广告
* True
* False

```
ampiriSDK.sdk.ShowTopBanner(size, gravity, true);
ampiriSDK.sdk.ShowBottomBanner(size, gravity, true);
```

要隐藏横幅：

```
ampiriSDK.sdk.HideTopBanner();
ampiriSDK.sdk.HideBottomBanner();
```

＃横幅回调例子
```
using UnityEngine;
using UnityEngine.UI;
using System.Collections;

/// <summary>
/// User Example Class on Banners
/// </summary>
public class BannerCallBack : MonoBehaviour {

    [SerializeField]
    Text log;

    void Log(string Text)
    {
        log.text = Text;
    }

    #region Mono
    void OnEnable()
    {
        // Add Standard Banner CallBacks
        AmpiriEvents.Banner_OnLoaded += OnBannerLoaded;
        AmpiriEvents.Banner_OnLoadError += OnBannerLoadError;
        AmpiriEvents.Banner_OnClick += OnBannerClicked;
        AmpiriEvents.Banner_OnNoAd += OnBannerNoAd;
    }
    void OnDisable()
    {
        // Remove Standard Banner CallBacks
        AmpiriEvents.Banner_OnLoaded -= OnBannerLoaded;
        AmpiriEvents.Banner_OnLoadError -= OnBannerLoadError;
        AmpiriEvents.Banner_OnClick -= OnBannerClicked;
        AmpiriEvents.Banner_OnNoAd -= OnBannerNoAd;
    }
    #endregion

    #region Banner CallBacks

	// Callback triggers when banner has successfully loaded
    public void OnBannerLoaded()
    {
        Log("Banner_OnLoaded");
    }
	// Callback triggers when banner has been clicked
    void OnBannerClicked()
    {
        Log("Fullscreen_OnBannerClick");
    }
    // Callback triggers when loading of banner returns error
    void OnBannerLoadError()
    {
        Log("Banner_OnLoadError");
    }
    // Callback triggers when loading of banner returns no advertisements
    void OnBannerNoAd()
    {
        Log("Banner_OnNoAd");
    }
    #endregion
}
```

集合插屏广告
==========
横幅大小是自动根据不同的屏幕尺寸定义的。

要*加载*插屏广告
```
ampiriSDK.sdk.LoadFullscreen();
```
To *show* interstitial banner:
```
ampiriSDK.sdk.ShowFullscreen();
```
To *load* and *show* interstitial banners:
```
ampiriSDK.sdk.LoadAndShowFullscreen();
```
To *load* and *show* interstitial banners with delay:
```
ampiriSDK.sdk.LoadAndShowWithDelayFullscreen();
```
# 插屏回调例子
```
using UnityEngine;
using UnityEngine.UI;
using System.Collections;

/// <summary>
/// Interstitial callbacks example
/// </summary>
public class InterstitialCallbacks : MonoBehaviour {

    [SerializeField]
    Text statusText;

    [SerializeField]
    FunctionalTestingExample fntest;

    [SerializeField]
    Button showButton;

    [SerializeField]
    Button loadnshowButton;

    [SerializeField]
    Button loadnshowDelayButton;

    void Log(string text)
    {
        statusText.text = "Current status: " + text;
    }

    void OnEnable()
    {
        AmpiriEvents.Interstitial_OnBannerLoaded += OnFullScreenBannerLoaded;
        AmpiriEvents.Interstitial_OnBannerLoadError += OnFullScreenBannerLoadError;
        AmpiriEvents.Interstitial_OnBannerClick += OnFullScreenBannerClicked;
        AmpiriEvents.Interstitial_OnBannerClose += OnFullScreenBannerClose;
        AmpiriEvents.Interstitial_OnBannerNoAd += OnFullScreenBannerNoAd;
    }

    void OnDisable()
    {

        AmpiriEvents.Interstitial_OnBannerLoaded -= OnFullScreenBannerLoaded;
        AmpiriEvents.Interstitial_OnBannerLoadError -= OnFullScreenBannerLoadError;
        AmpiriEvents.Interstitial_OnBannerClick -= OnFullScreenBannerClicked;
        AmpiriEvents.Interstitial_OnBannerClose -= OnFullScreenBannerClose;
        AmpiriEvents.Interstitial_OnBannerNoAd -= OnFullScreenBannerNoAd;

    }

	// Callback triggers when Interstitial has successfully loaded
    void OnFullScreenBannerLoaded()
    {
        Log("FullScreen_OnBannerLoaded");
        showButton.interactable = true;
        loadnshowButton.interactable = loadnshowDelayButton.interactable = false;
    }
	// Callback triggers when Interstitial has closed
    void OnFullScreenBannerClose()
    {
        Log("Fullscreen_OnBannerClose");
        fntest.ExitTransition();
        showButton.interactable = false;
        loadnshowButton.interactable = loadnshowDelayButton.interactable = true;
    }
	// Callback triggers when Interstitial has been clicked
    void OnFullScreenBannerClicked()
    {
        Log("Fullscreen_OnBannerClick");
        fntest.ExitTransition();
        showButton.interactable = false;
        loadnshowButton.interactable = loadnshowDelayButton.interactable = true;
    }
	// Callback triggers when Interstitial returns and error while loading
    void OnFullScreenBannerLoadError()
    {
        Log("Fullscreen_OnBannerLoadError");
        fntest.ExitTransition();
        showButton.interactable = false;
        loadnshowButton.interactable = loadnshowDelayButton.interactable = true;
    }
	// Callback triggers when Interstitial has no advertisments
    void OnFullScreenBannerNoAd()
    {
        Log("Fullscreen_OnBannerNoAd");
        fntest.ExitTransition();
        showButton.interactable = false;
        loadnshowButton.interactable = loadnshowDelayButton.interactable = true;
    }

    void Awake()
    {
        showButton.interactable = false;
    }

}

```

集合视频广告
==========
要*加载*视频广告
```
ampiriSDK.sdk.LoadVideo();
```
Once a video has been successfully loaded, you can use the callback AmpiriEvents.video_OnBannerLoaded
to show video:

```
if (ampiriSDK.sdk.VideoIsReady())
{
    Log("Video_OnBannerLoaded ready YES");
    ampiriSDK.sdk.ShowVideo();
}
else

    Log("Video_OnBannerLoaded ready NO");
}
```

# 视频广告回调例子
```
using UnityEngine;
using UnityEngine.UI;
using System.Collections;

/// <summary>
/// Class handles or video callbacks
/// </summary>
public class VideoCallBack : MonoBehaviour {

    [SerializeField]
    AdsSdk ampiriSDK;

    [SerializeField]
    FunctionalTestingExample fntest; 

    [SerializeField]
    Text text;

    void OnEnable()
    {
        AmpiriEvents.video_OnBannerLoaded += OnVideoBannerLoaded;
        AmpiriEvents.video_OnBannerOpen += OnVideoBannerOpen;
        AmpiriEvents.video_OnVideoStarted += OnVideoStarted;
        AmpiriEvents.video_OnBannerLoadError += OnVideoBannerLoadError;
        AmpiriEvents.video_OnBannerClose += OnVideoBannerClose;
        AmpiriEvents.video_OnVideoCompleted += OnVideoCompleted;
        AmpiriEvents.video_OnBannerNoAd += OnVideoNoAd;
    }

    void OnDisable()
    {
        AmpiriEvents.video_OnBannerLoaded -= OnVideoBannerLoaded;
        AmpiriEvents.video_OnBannerOpen -= OnVideoBannerOpen;
        AmpiriEvents.video_OnVideoStarted -= OnVideoStarted;
        AmpiriEvents.video_OnBannerLoadError -= OnVideoBannerLoadError;
        AmpiriEvents.video_OnBannerClose -= OnVideoBannerClose;
        AmpiriEvents.video_OnVideoCompleted -= OnVideoCompleted;
        AmpiriEvents.video_OnBannerNoAd -= OnVideoNoAd;
    }


    void Log(string Text)
    {
        if(text != null)
            text.text = Text;
    }

    // Callback triggers when video has loaded
    void OnVideoBannerLoaded()
    {
        if (ampiriSDK.sdk.VideoIsReady())
        {
            Log("Video_OnBannerLoaded ready YES");
            ampiriSDK.sdk.ShowVideo();
        }
        else
       
            Log("Video_OnBannerLoaded ready NO");
        }
    }
	// Callback triggers when video has opened
    void OnVideoBannerOpen()
    {
        Log("Video_OnBannerOpen");
    }
    // Callback triggers when video closes
    void OnVideoBannerClose()
    {
        Log("Video_OnBannerClose");
    }
	// // Callback triggers when loading of video has returned an error
    void OnVideoBannerLoadError()
    {
        Log("Video_OnBannerLoadError");

        fntest.ExitTransition();
    }
	// // Callback triggers when video has completed
    void OnVideoCompleted()
    {
        Log("Video_OnVideoCompleted");
        fntest.ExitTransition();
    }
	// Callback triggers when video has started
    void OnVideoStarted()
    {
        Log("Video_OnVideoStarted");
    }

	// Callback triggers when video has no advertisments
    void OnVideoNoAd()
    {
        Log("Video_OnVideoNoAd");
    }

}
```
集合第三方SDKs
============
#### Android

对于Android集合第三方SDKs，请按照下面的步骤[平台特定步骤](#平台特定步骤)。

#### iOS
1. 请下载在third-party 文件夹里的unity packages。
2. 当下载完毕时, 双击 &lt;network&gt;.unitypackage 来输入所有文件.

**注意: 您不能在一个应用程序同时使用百度和MoPub。因为会导致编译错误。**

平台特定步骤
==========

#### Android
* 要运行示例Android应用，进口AmpiriUnityPluginTestApp_Android项目到Android Studio并运行项目。
* 要运行Unity App，请按照“构建和运行Android Ampiri SDK **”中的说明操作。 

#### iOS
1. 转到__File> Build Settings__，将想要包含在构建中的场景拖放到Build__中的__Scenes。
2. 在__Platform__部分，选择__iOS__. 配置构建于__Platform__部分的权利，并在menu中选择__Build__。
> **注意: 您可能会被要求下载并安装 __iOSSupport Installer__.**
3. 选择您要建立的目标目录，点击__Save__.
4. 打开Xcode中导出的项目。

## 构建并运行Android Ampiri SDK
如果要运行Ampiri SDK 在Unity Android版，请按照以下的步骤：

1. 点击 __File > Build Settings__.
2. 在__Platforms__列表中选择__Android__并点击__Google的Android Project__复选框。从您的unity项目里拖所需的scenes到__Scenes In Build__。
3. 点击 __Player Settings__并输入__Bundle Identifier__.
4. 点击 __Export__ 按钮并选择项目的目的地。
5. 下载并安装[Android Studio]（https://developer.android.com/studio/index.html）。
6. 在Android Studio里点击__File > New > Import Project__. 选择您在步骤4中指定的导出的项目的目的。
7. 根据[Ampiri SDK Instructions](https://github.com/ampiri/ampiri-android-sdk)配置项目。

## 构建并运行iOS Ampiri SDK

如果要运行Ampiri SDK 在Unity IOS版，请按照以下的步骤：

1. 点击 __File > Build Settings__.
2. 在__Platforms__列表中选择__iOS__。从您的unity项目里拖所需的scenes到__Scenes In Build__。
3. 点击 __Player Settings__并输入__Bundle Identifier__.
4，寻找__Target minium iOS Version__ 并选择 __8.0__。
5. 点击 __Export__ 按钮并选择项目的目的地。
6. 打开Xcode中导出的项目。
7. 点击项目并在 __General Tab__里配置 __signing__.
8. 向下滚动，寻找__Embeddded Binaries__，选择__“+”__按钮并选择__Plugins > iOS > AMPVastLib.Framework__。
9. 在顶部Xcode中，选择__Product > Build__

