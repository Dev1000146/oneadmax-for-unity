---
description: 보상을 제공하는 비디오 광고 입니다. 비디오 광고 종료시 Complete event callback 으로 광고 참여 완료 정보를 전달합니다.
---

# 보상형 비디오 광고 구현하기

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

### 유저 식별값 입력

유저 식별값은 보상형 비디오 시청 완료 시 완료 유저를 식별하기 위해 사용되는 값입니다.

```csharp
string userId = "bXlBY2NvdW50X25hbWU";
ONEAdMaxClient.SetUserId(userId);
```

{% hint style="warning" %}
#### 주의사항

1. 1명의 유저는 1개의 고유한 유저식별값을 가져야하며, 가변적인 값을 사용해서는 안됩니다.
2. 개인정보( 이메일, 이름, 전화번호, 식별가능한 유저아이디 등 )이 포함되어서는 안됩니다.
3. 한글, 특수문자, 공백 등이 포함된 경우에는 반드시 URL 인코딩 처리를 하여 사용하여야 합니다.
4. 유저가 광고에 진입하기 전에 설정되어야 합니다.
{% endhint %}

## 보상형 비디오 광고 예제

아래 샘플 코드는 보상형 비디오 광고 사용 방법에 대해 자세히 설명합니다.\
`OAMRewardVideo` 인스턴스를 생성하고, 이벤트를 처리하여 기능을 확장할 수 있습니다.

### 보상형 비디오 광고 인스턴스 생성하기

아래는 보상형 비디오 광고의 인스턴스를 생성하기 위한 예제입니다.

```csharp
public class RewardVideoAdController : MonoBehaviour
{
    OAMRewardVideo _rewardVideoAd
    
    /// <summary>
    /// Creates the ad.
    /// </summary>
    public void CreateRewardVideoAd()
    {
        Debug.Log("Creating Reward video ad.");
        
        if (_rewardVideoAd != null)
        {
            Debug.LogWarning("Already have a Reward video ad.");
            return;
        }
    
        _rewardVideoAd = new OAMRewardVideo();
    }
}
```

### 네트워크 스케쥴 타임아웃 설정 (선택사항)

광고에 대한 네트워크 스케쥴 타임아웃을 설정합니다. (기본값: 5초)\
광고 로딩 시 각 네트워크 별로 타임아웃 시간을 주어 해당 시간 안에 광고를 받지 못할 경우, 다음 네트워크로 넘어가게 됩니다.

```csharp
_rewardVideoAd.SetNetworkScheduleTimeout(5); // Default 5 seconds
```

### 보상형 비디오 광고의 이벤트 청취하기 (선택사항)

보상형 비디오 광고를 불러올 때 발생하는 이벤트에 대한 리스너를 설정합니다.

```csharp
/// <summary>
/// Register to events the reward video ad may raise.
/// </summary>
private void RegisterEvents(OAMRewardVideo ad)
{
    // Raised when an ad is loaded into the reward video ad.
    ad.OnLoaded += () =>
    {
        Debug.Log("Reward video ad loaded.");
    };

    // Raised when an ad fails to load into the reward video ad.
    ad.OnLoadFailed += (OAMError error) =>
    {
        Debug.LogError("Reward video ad failed to load an ad with error : " + error);
    };

    // Raised when an ad opened full screen content.
    ad.OnOpened += () =>
    {
        Debug.Log("Reward video ad has been opened.");
    };

    // Raised when the ad failed to open full screen content.
    ad.OnOpenFailed += (OAMError error) =>
    {
        Debug.LogError("Reward video ad failed to open an ad with error : " + error);
    };

    // Raised when the ad closed full screen content.
    ad.OnClosed += () =>
    {
        Debug.LogError("Reward video ad is closed.");
    };

    // Raised when the ad completed full screen content.
    ad.OnCompleted += (int adNetworkNo, bool compledted) =>
    {
        Debug.Log("Reward video ad completed : " + "adNetworkNo=" + adNetworkNo + ", compledted=" + compledted);
    };

    // Raised when a click is recorded for an ad.
    ad.OnClicked += () =>
    {
        Debug.Log("Reward video ad was clicked.");
    };
}
```

[oam-error-code.md](oam-error-code.md "mention")

### 보상형 비디오 광고 만들기

모든 설정을 완료하고 `OAMInterstitialVideo`를 만듭니다.

```csharp
// These ad units are configured to always serve test ads.   
private readonly string _placementId = "zgGP6LbMBnaGB7G";

_rewardVideoAd.Create(_placementId);
```

### 보상형 비디오 광고 로드 하기

보상형 비디오 광고 `Load()` API를 호출하여 서버에 광고를 요청합니다.

```csharp
/// <summary>
/// Loads the ad.
/// </summary>
public void Load()
{
    if (_rewardVideoAd == null)
    {
        Debug.LogWarning("The reward video ad is null.");
        return;
    }

    _rewardVideoAd.Load();
}
```

{% hint style="warning" %}
광고 호출에 대한 결과로 광고 수신에 실패한 경우 Load()를 재호출 하면 안됩니다. 과도한 광고 요청은 차단 사유가 됩니다.
{% endhint %}

### 보상형 비디오 광고 노출하기

로드가 완료되면 광고를 노출을 원하는 시점에 `Show()` API를 호출하여 화면에 노출 시킵니다.

```csharp
/// <summary>
/// Shows the ad.
/// </summary>
public void Show()
{
    if (_rewardVideoAd != null && _rewardVideoAd.IsLoaded())
    {
        Debug.Log("Showing reward video ad.");
        _rewardVideoAd.Show();
    }
    else
    {
        Debug.LogError("The reward video ad hasn't loaded yet.");
    }
}
```

### 보상형 비디오 광고 메모리 해지하기

보상형 광고의 인스턴스 내의 메모리를 해지 합니다.

```csharp
/// <summary>
/// Destroys the ad.
/// </summary>
public void Destroy()
{
    if (_rewardVideoAd != null)
    {
        Debug.Log("Destroying reward video ad.");
        _rewardVideoAd.Destroy();
        _rewardVideoAd = null;
    }
}
```

### 보상형 비디오 광고 로드 여부 체크하기

보상형 비디오 광고의 로드 여부를 체크합니다. (return ture | false)

```csharp
_rewardVideoAd.IsLoaded();
```

### 어플리케이션의 라이프사이클 확장하기

배너 광고가 노출되고 있는 어플리케이션의 `onPause` / `onResume`여부를 체크하기 위해 구현해야 합니다.

```csharp
public class RewardVideoAdController : MonoBehaviour
{
    void OnApplicationPause(bool pauseStatus)
    {
        // You should call events for pause and resume in the application lifecycle.
        _rewardVideoAd?.OnPause(pauseStatus);
    }
}
```

{% hint style="warning" %}
#### 주의사항

처리하지 않았을 경우 third-party mediation을 사용에서 리포트 수치가 누락되는 경우가 발생합니다.
{% endhint %}

### 보상형 비디오 광고 CS 페이지 호출

보상형 비디오 관련 CS 접수 페이지를 노출 시키기 위해서는 아래 API를 호출하여 줍니다.

```csharp
string userId = "bXlBY2NvdW50X25hbWU";
ONEAdMaxClient.OpenRewardVideoCSPage(userId);
```
