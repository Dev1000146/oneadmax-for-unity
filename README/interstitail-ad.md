---
description: 전면 광고는 화면 전체를 덮는 형태의 광고입니다.
---

# 전면 광고 구현하기

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

## 전면 광고 예제

아래 샘플 코드는 전면 광고 사용 방법에 대해 자세히 설명합니다.\
`OAMInterstitial` 인스턴스를 생성하고, 이벤트를 처리하여 기능을 확장할 수 있습니다.

### 전면 광고 인스턴스 생성하기

아래는 전면 광고의 인스턴스를 생성하기 위한 예제입니다.

```csharp
public class InterstitialAdController : MonoBehaviour
{   
    OAMInterstitial _interstitialAd;
    
    /// <summary>
    /// Creates the ad.
    /// </summary>
    public void CreateInterstitialAd()
    {
        Debug.Log("Creating Interstitial ad.");
        
        if (_interstitialAd != null)
        {
            Debug.LogWarning("Already have a Interstitial ad.");
            return;
        }

        _interstitialAd = new OAMInterstitial();
    }
}
```

### 전면 광고 화면의 커스텀 옵션 (선택사항)

```csharp
/// <summary>
/// Customization of the ads screen.
/// </summary>
/// <seealso cref="CustomExtraKey"/>
/// <param name="ad"><see cref="OAMInterstitial"/></param>
private void SetCustomExtras(OAMInterstitial ad)
{
    var customExtras = new Dictionary<CustomExtraKey, object>
    {
        // Change interstitial ad background color and transparency (Defaults to "#FF000000")
        { CustomExtraKey.BACKGROUND_COLOR, "#FF000000" },

        // Set whether to show a close button in the top right corner of the interstitial ad. (Defaults to false)
        { CustomExtraKey.HIDE_CLOSE_BTN, false },

        // Change to False to provide a margin based on the ad image. (Defaults to true)
        { CustomExtraKey.CLOSE_BTN_MARGIN_FROM_EDGE, true },
        // Left margin of the interstitial ad close button. (Defaults to -28dp)
        { CustomExtraKey.CLOSE_BTN_LEFT_MARGIN, -28 },
        // Top margin of the interstitial ad close button. (Defaults to 20dp)
        { CustomExtraKey.CLOSE_BTN_TOP_MARGIN, 20 },
        // Right margin of the interstitial ad close button. (Defaults to 20dp)
        { CustomExtraKey.CLOSE_BTN_RIGHT_MARGIN, 20 },
        // Bottom margin of the interstitial ad close button. (Defaults to 0dp)
        { CustomExtraKey.CLOSE_BTN_BOTTOM_MARGIN, 0 },
        
        // Disable interstitial ad exit with a backkey. (Defaults to false)
        { CustomExtraKey.DISABLE_BACK_BTN, false },

        // Whether to show an exit message in interstitial ads. (Defaults to false)
        { CustomExtraKey.IS_ENDING_AD, false },
        // Change the exit ad message.
        { CustomExtraKey.ENDING_TEXT, "To exit, press the Back button one more time." },
        // Change the text size of the exit ad message.
        { CustomExtraKey.ENDING_TEXT_SIZE, 14 },
        // Change the text color of the exit ad message.
        { CustomExtraKey.ENDING_TEXT_COLOR, "#FFFFFFFF" },
        // Change the gravity of exit ad message.
        { CustomExtraKey.ENDING_TEXT_GRAVITY, OAMGravity.RIGHT },
    };

    ad.SetCustomExtras(customExtras);
}
```

### 전면 광고의 이벤트 청취하기 (선택사항)

전면 광고를 불러올 때 발생하는 이벤트에 대한 리스너를 설정합니다.

```csharp
/// <summary>
/// Register to events the interstital ad may raise.
/// </summary>
private void RegisterEvents(OAMInterstitial ad)
{
    // Raised when an ad is loaded into the interstitial ad.
    ad.OnLoaded += () =>
    {
        Debug.Log("Interstitial ad loaded.");
    };

    // Raised when the interstitial ad fails to load.
    ad.OnLoadFailed += (OAMError error) =>
    {
        Debug.LogError("Interstitial ad failed to load an ad with error : " + error);
    };

    // Raised when an ad opened full screen content.
    ad.OnOpened += () =>
    {
        Debug.Log("Interstitial ad has been opened.");
    };

    // Raised when the ad failed to open full screen content.
    ad.OnOpenFailed += (OAMError error) =>
    {
        Debug.LogError("Interstitial ad failed to open an ad with error : " + error);
    };

    // Raised when the ad closed full screen content.
    ad.OnClosed += (CloseEventType closeEventType) =>
    {
        Debug.LogError("Interstitial ad closed with type : " + closeEventType);
    };

    // Raised when a click is recorded for an ad.
    ad.OnClicked += () =>
    {
        Debug.Log("Interstitial ad was clicked.");
    };
}
```

[oam-error-code.md](oam-error-code.md "mention")

### 전면 광고 만들기

모든 설정을 완료하고 `OAMInterstitial`를 만듭니다.

```csharp
// These ad units are configured to always serve test ads.   
private readonly string _placementId = "zOVONpwccfpWVCq";

_interstitialAd.Create(_placementId);
```

### 전면 광고 로드 하기

전면 광고 `Load()` API를 호출하여 서버에 광고를 요청합니다.

```csharp
/// <summary>
/// Loads the ad.
/// </summary>
public void Load()
{
    if (_interstitialAd == null)
    {
        Debug.LogWarning("The interstital ad is null.");
        return;
    }

    _interstitialAd.Load();
}
```

{% hint style="warning" %}
광고 호출에 대한 결과로 광고 수신에 실패한 경우 Load()를 재호출 하면 안됩니다. 과도한 광고 요청은 차단 사유가 됩니다.
{% endhint %}

### 전면 광고 노출하기

로드가 완료되면 광고를 노출을 원하는 시점에 `Show()` API를 호출하여 화면에 노출 시킵니다.

```csharp
/// <summary>
/// Shows the ad.
/// </summary>
public void Show()
{
    if (_interstitialAd != null && _interstitialAd.IsLoaded())
    {
        Debug.Log("Showing interstital ad.");
        _interstitialAd.Show();
    }
    else
    {
        Debug.LogError("The Interstitial ad hasn't loaded yet.");
    }
}
```

### 전면 광고 메모리 해지하기

전면 광고의 인스턴스 내의 메모리를 해지 합니다.

```csharp
/// <summary>
/// Destroys the ad.
/// </summary>
public void Destroy()
{
    if (_interstitialAd != null)
    {
        Debug.Log("Destroying interstitial ad.");
        _interstitialAd.Destroy();
        _interstitialAd = null;
    }
}
```

### 전면 광고 로드 여부 체크하기

전면 광고의 로드 여부를 체크합니다. (return ture | false)

```csharp
_interstitialAd.IsLoaded();
```
