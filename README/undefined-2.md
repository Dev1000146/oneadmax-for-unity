---
description: 전면 광고와 같은 형태로 화면 전체를 덮는 광고입니다. 다만 전면 광고와 달리 동영상을 노출합니다.
---

# 전면 비디오 광고 구현하기

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

## 전면 비디오 광고 예제

아래 샘플 코드는 전면 비디오 광고 사용 방법에 대해 자세히 설명합니다.\
`OAMInterstitialVideo` 인스턴스를 생성하고, 이벤트를 처리하여 기능을 확장할 수 있습니다.

### 전면 비디오 광고 인스턴스 생성하기

아래는 전면 비디오 광고의 인스턴스를 생성하기 위한 예제입니다.

{% code fullWidth="false" %}
```csharp
public class InterstitialVideoAdController : MonoBehaviour
{
    OAMInterstitialVideo _interstitialVideoAd;
    
    /// <summary>
    /// Creates the ad.
    /// </summary>
    public void CreateInterstitialVideoAd()
    {
        Debug.Log("Creating Interstitial video ad.");
        
        if (_interstitialVideoAd != null)
        {
            Debug.LogWarning("Already have a Interstitial video ad.");
            return;
        }

        _interstitialVideoAd = new OAMInterstitialVideo();
    }
}
```
{% endcode %}

### 네트워크 스케쥴 타임아웃 설정 (선택사항)

광고에 대한 네트워크 스케쥴 타임아웃을 설정합니다. (기본값: 5초)\
광고 로딩 시 각 네트워크 별로 타임아웃 시간을 주어 해당 시간 안에 광고를 받지 못할 경우, 다음 네트워크로 넘어가게 됩니다.

```csharp
_interstitialVideoAd.SetNetworkScheduleTimeout(5); // Default 5 seconds
```

### 전면 비디오 광고의 이벤트 청취하기 (선택사항)

전면 비디오 광고를 불러올 때 발생하는 이벤트에 대한 리스너를 설정합니다.

```csharp
/// <summary>
/// Register to events the interstital video ad may raise.
/// </summary>
private void RegisterEvents(OAMInterstitialVideo ad)
{
    // Raised when an ad is loaded into the interstitial ad.
    ad.OnLoaded += () =>
    {
        Debug.Log("Interstitial video ad loaded.");
    };

    // Raised when an ad fails to load into the interstitial ad.
    ad.OnLoadFailed += (OAMError error) =>
    {
        Debug.LogError("Interstitial video ad failed to load an ad with error : " + error);
    };

    // Raised when an ad opened full screen content.
    ad.OnOpened += () =>
    {
        Debug.Log("Interstitial video ad has been opened.");
    };

    // Raised when the ad failed to open full screen content.
    ad.OnOpenFailed += (OAMError error) =>
    {
        Debug.LogError("Interstitial video ad failed to open an ad with error : " + error);
    };

    // Raised when the ad closed full screen content.
    ad.OnClosed += () =>
    {
        Debug.LogError("Interstitial video ad is closed.");
    };
}
```

[oamerror.md](oamerror.md "mention")

### 전면 비디오 광고 만들기

모든 설정을 완료하고 `OAMInterstitialVideo`를 만듭니다.

```csharp
// These ad units are configured to always serve test ads.   
private readonly string _placementId = "zOVONpwccfpWVCq";

_interstitialVideoAd.Create(_placementId);
```

### 전면 비디오 광고 로드 하기

전면 비디오 광고 `Load()` API를 호출하여 서버에 광고를 요청합니다.

```csharp
/// <summary>
/// Loads the ad.
/// </summary>
public void Load()
{
    if (_interstitialVideoAd == null)
    {
        Debug.LogWarning("The interstital video ad is null.");
        return;
    }

    _interstitialVideoAd.Load();
}
```

{% hint style="warning" %}
광고 호출에 대한 결과로 광고 수신에 실패한 경우 Load()를 재호출 하면 안됩니다. 과도한 광고 요청은 차단 사유가 됩니다.
{% endhint %}

### 전면 비디오 광고 노출하기

로드가 완료되면 광고를 노출을 원하는 시점에 `Show()` API를 호출하여 화면에 노출 시킵니다.

```csharp
/// <summary>
/// Shows the ad.
/// </summary>
public void Show()
{
    if (_interstitialVideoAd != null && _interstitialVideoAd.IsLoaded())
    {
        Debug.Log("Showing interstital video ad.");
        _interstitialVideoAd.Show();
    }
    else
    {
        Debug.LogError("The Interstitial video ad hasn't loaded yet.");
    }
}
```

### 전면 비디오 광고 메모리 해지하기

전면 광고의 인스턴스 내의 메모리를 해지 합니다.

```csharp
/// <summary>
/// Destroys the ad.
/// </summary>
public void Destroy()
{
    if (_interstitialVideoAd != null)
    {
        Debug.Log("Destroying interstitial video ad.");
        _interstitialVideoAd.Destroy();
        _interstitialVideoAd = null;
    }
}
```

### 전면 비디오 광고 로드 여부 체크하기

전면 비디오 광고의 로드 여부를 체크합니다. (return ture | false)

```csharp
_interstitialVideoAd.IsLoaded();
```
