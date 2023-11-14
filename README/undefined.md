---
description: >-
  배너 광고는 화면의 한 부분을 차지하는 직사각형 이미지 또는 텍스트 광고입니다. 사용자가 앱과 상호작용하는 동안 화면에 지속적으로 노출
  되며, 자동으로 새로고침 됩니다.
---

# 배너 광고 구현하기

## 전제 조건

{% content-ref url="../" %}
[..](../)
{% endcontent-ref %}

## 테스트

todo 테스트 설정

## ONEAdMax SDK 초기화

광고를 로드하기 전에 앱이 ONEAdMax를 초기화 해야합니다.\
`ONEAdMaxClient.Initialize()` 초기화는 최초 한 번만 수행해야 합니다.

다음은 ONEAdMaxClient 를 초기화 하는 방법입니다.

```csharp
...
using ONEAdMax;
...
public class ONEAdMaxDemo : MonoBehaviour
{
    private static bool _isInitialized = false;
    
    void Start()
    {
        if (!_isInitialized)
        {
            // Initialize the ONEAdMax SDK.
            ONEAdMaxClient.Initialize(() =>
            {
                // This callback is called once the ONEAdMax SDK is initialized.
                _isInitialized = true
            });
        }
    }
}
```

## 배너 광고 예제

아래 샘플 코드는 배너 뷰 사용 방법에 대해 자세히 설명합니다.\
`OAMBannerView` 인스턴스를 생성하고, 라이프사이클 이벤트를 처리하여 기능을 확장할 수 있습니다.

### 배너 뷰 인스턴스 생성하기

아래는 배너 뷰의 인스턴스를 생성하기 위한 예제입니다.

```csharp
using ONEAdMax;
...
public class BannerViewController : MonoBehaviour
{    
    OAMBannerView _bannerView;
    
    public void CreateBannerView()
    {
        Debug.Log("Creating banner view.");

        // If we already have a banner.
        if (_bannerView != null) {
            Debug.LogWarning("Already have a banner.");
            return;
        }

        // Create a 320x50 banner view at top of the screen.
        _bannerView = new OAMBannerView(AdSize.BANNER_320x50, AdPosition.Top);
    }
}
```

배너 뷰 생성자의 매개변수:

* `AdSize`: 사용하고자 하는 광고의 크기.
* `AdPosition`: 배너 뷰를 배치해야 하는 위치.

#### 배너 크기

아래 표에는 표준 배너 크기가 나와 있습니다.

<table><thead><tr><th>크기</th><th>상수</th><th data-hidden>상수</th><th data-hidden></th></tr></thead><tbody><tr><td>320x50</td><td>AdSize.BANNER_320x50</td><td>BANNER_320x50</td><td></td></tr><tr><td>300x250</td><td>AdSize.BANNER_300x250</td><td>BANNER_300x250</td><td></td></tr><tr><td>320x100</td><td>AdSize.BANNER_320x100</td><td>BANNER_320x100</td><td></td></tr></tbody></table>

### 애니메이션 효과 설정 (선택사항)

배너가 노출될 때의 애니메이션을 설정합니다. (기본값: `AnimType.NONE`)\
총 7가지의 애니메이션을 설정할 수 있습니다.

```csharp
_bannerView.SetAnimType(AnimType.SLIDE_LEFT); // Defaults to AnimType.NONE
```

아래 표는 애니메이션 타입의 종류가 나와 있습니다.

<table><thead><tr><th width="261">상수</th><th>설명</th><th data-hidden>설명</th><th data-hidden></th></tr></thead><tbody><tr><td><code>AnimType.NONE</code></td><td>애니메이션 없음 (기본값)</td><td></td><td></td></tr><tr><td><code>AnimType.FADE_IN</code></td><td>페이드 인 애니메이션</td><td></td><td></td></tr><tr><td><code>AnimType.SLIDE_LEFT</code></td><td>왼쪽으로 슬라이드 애니메이션</td><td></td><td></td></tr><tr><td><code>AnimType.SLIDE_RIGHT</code></td><td>오른쪽으로 슬라이드 애니메이션</td><td></td><td></td></tr><tr><td><code>AnimType.TOP_SLIDE</code></td><td>윗쪽으로 슬라이드 애니메이션</td><td></td><td></td></tr><tr><td><code>AnimType.BOTTOM_SLIDE</code></td><td>아래쪽으로 슬라이드 애니메이션</td><td></td><td></td></tr><tr><td><code>AnimType.CIRCLE</code></td><td>배너 회전 애니메이션</td><td></td><td></td></tr></tbody></table>

### 네트워크 스케쥴 타임아웃 설정 (선택사항)

광고에 대한 네트워크 스케쥴 타임아웃을 설정합니다. (기본값: 5초)\
광고 로딩 시 각 네트워크 별로 타임아웃 시간을 주어 해당 시간 안에 광고를 받지 못할 경우, 다음 네트워크로 넘어가게 됩니다.

```csharp
_bannerView.SetNetworkScheduleTimeout(5); // Default 5 seconds
```

### 배너 광고 요청 갱신주기 설정 (선택사항)

배너 광고에 대한 갱신주기를 설정합니다. (기본값: 60초)\
설정 가능 범위는 15 \~ 300초 사이이며 -1로 설정 시 자동으로 갱신되지 않습니다.

```csharp
// The default is 60 seconds, with a setting range of 15 to 300 seconds.
_bannerView.SetRefreshTime(60);
```

### 배너 배경색 채우기 (선택사항)

배너 뷰의 빈 공간에 배경색을 채울 수 있습니다. (기본값: true)

```csharp
_bannerView.SetAutoBgColor(true); // Defaults to true
```

### 배너 미디에이션 옵션 설정 (선택사항)

배너 광고의 일부 미디에이션에 대한 설정 값을 지원합니다.

```csharp
/// <summary>
/// Extend the functionality of the mediation that appears in your ads.
/// </summary>
/// <seealso cref="MediationKey" />
/// <param name="ad"><see cref="OAMBannerView"/></param>
private void SetMediationExtras(OAMBannerView ad)
{
    var extras = new Dictionary<MediationKey, object>
    {
        // Set to true to show Cauly ads on the lock screen. (Defaults to false)
        { MediationKey.CAULY_ENABLE_LOCK, false },
        
        // Control ad serving intervals on Cauly's side. (Defaults to true)
        { MediationKey.CAULY_ENABLE_DYNAMIC_RELOAD_INTERVAL, true },
        
        // Controls how often ads appear in media. (Default 20 seconds)
        // (available after changing CAULY_DAYNAMIC_RELOAD_INTERVAL to False)
        { MediationKey.CAULY_RELOAD_INTERVAL, 20 },
        
        // Set thread priority.
        { MediationKey.CAULY_THREAD_PRIORITY, 5 },
        
        // Options to set age information for meso ads. (Defaults to -1)
        // Unknown = -1; Children(under 13) = 0; Teens and adults(over 13) = 1
        { MediationKey.MEZZO_AGE_LEVEL, -1 },
        
        // Option to allow the banner to run in the background. (true | false)
        { MediationKey.MEZZO_ENABLE_BACKGROUND_CHECK, false },
        
        // The app's Store URL
        { MediationKey.MEZZO_STORE_URL, "..." }
    };

    ad.SetMediationExtras(extras);
}
```

[<mark style="color:red;">// 미디에이션 설정 방법 링크</mark>](https://wiki.onestorecorp.com/display/ONEIAA/ONE+AdMax+SDK#ONEAdMaxSDK-%EB%AF%B8%EB%94%94%EC%97%90%EC%9D%B4%EC%85%98\(Mediation\))

### 배너 뷰의 이벤트 청취하기 (선택사항)

배너 광고를 불러올 때 발생하는 이벤트에 대한 리스너를 설정합니다.

```csharp
/// <summary>
/// Register to events the banner may raise.
/// </summary>
private void RegisterEvents(OAMBannerView ad)
{
    // Raised when an ad is loaded into the banner view.
    ad.OnLoaded += () =>
    {
        Debug.Log("Banner view loaded.");
    };

    // Raised when an ad fails to load into the banner view.
    ad.OnLoadFailed += (OAMError error) =>
    {
        Debug.LogError("Banner view failed to load an ad with error : " + error);
    };

    // Raised when a click is recorded for an ad.
    ad.OnClicked += () =>
    {
        Debug.Log("Banner view was clicked.");
    };
}
```

[oamerror.md](oamerror.md "mention")

### 배너 뷰를 화면에 올리기

모든 설정을 완료하고 `OAMBannerView`를 실질적으로 화면에 올립니다.

```csharp
// These ad units are configured to always serve test ads.   
private readonly string _placementId = "HetrwLiENeBtT7q";  // 320 * 50

_bannerView.Create(_placementId);
```

### 배너 로드 하기

배너 광고 노출을 원하는 시점에 `Load()` API를 호출하여 서버에 광고를 요청합니다.

```csharp
/// <summary>
/// Creates the banner view and loads a banner ad.
/// </summary>
public void Load()
{
    if (_bannerView == null)
    {
        Debug.LogWarning("The banner view is null.");
        return;
    }

    _bannerView.Load();
}
```

{% hint style="warning" %}
광고 호출에 대한 결과로 광고 수신에 실패한 경우 Load()를 재호출 하면 안됩니다. 과도한 광고 요청은 차단 사유가 됩니다.
{% endhint %}

### 배너 광고 중단하기

배너 광고 노출을 더 이상 원하지 않는 시점에 호출합니다.

```csharp
/// <summary>
/// Destroys the ad.
/// When you are finished with a OAMBannerView, make sure to call
/// the Destroy() method before dropping your reference to it.
/// </summary>
public void Destroy()
{
    if (_bannerView != null)
    {
        Debug.Log("Destroying banner view.");
        _bannerView.Destroy();
        _bannerView = null;
    }
}
```

{% hint style="warning" %}
단, 어플리케이션이 Pause 상태일 경우 Destroy()를 호출할 경우 클릭 리포트가 누락되는 경우가 발생함으로 배너 뷰가 포함된 Activity 또는 Fragment가 destroy 될 때 호출해야 합니다.
{% endhint %}

### 배너 광고 로드 여부 체크하기

배너 광고의 로드 여부를 체크합니다. (return ture | false)

```csharp
_bannerView.IsLoaded();
```

### 어플리케이션의 라이프사이클 확장하기

배너 광고가 노출되고 있는 어플리케이션의 `onPause` / `onResume`여부를 체크하기 위해 구현해야 합니다.

```csharp
public class BannerViewController : MonoBehaviour
{
    void OnApplicationPause(bool pauseStatus)
    {
        // You should call events for pause and resume in the application lifecycle.
        _bannerView?.OnPause(pauseStatus);
    }
}
```

{% hint style="warning" %}
#### 주의사항

처리하지 않았을 경우 third-party mediation을 사용에서 리포트 수치가 누락되는 경우가 발생합니다.
{% endhint %}
