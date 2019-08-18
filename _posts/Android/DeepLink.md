<!--
1. 딥링크란 무엇인가
2. 딥링크 관련 개념 (비교?)
    URI 스킴의 한계로 인해 앱이 설치되어있지 않은 경우 에러페이지를 보여줄 수밖에 없다??? 그리고 URI 충돌이 발생할 수 있다???(https://blog.branch.io/ko/%EC%9C%A0%EB%8B%88%EB%B2%84%EC%84%A4-%EB%A7%81%ED%81%AC-uri-%EC%8A%A4%ED%82%B4-%EC%95%B1-%EB%A7%81%ED%81%AC-%EB%B0%8F-%EB%94%A5-%EB%A7%81%ED%81%AC-%EB%AC%B4%EC%8A%A8-%EC%B0%A8%EC%9D%B4%EA%B0%80/)
    1) 유니버셜 링크(URI 스킴 설명도 같이 하쟈)
    2) 앱 링크
    3) 모바일 딥링크(앱 개발에서 '딥링크'라고 하면 주로 이것을 의미한다)
    4) 디퍼드 딥링크
    5) 다이나믹 링크(파이어베이스)
    6) contextual deep link?????????
3. 그래서 우리는 무엇을 써야 하는가?
- 마보에서는 마케팅을 위해 딥링크를 사용(아마도?)
- 사용자가 클릭했을 때 지연이 없어야 함
- 광고별/플랫폼별로 유입을 추적할 수 있어야 함
-->


## 딥링크(Deep Link)란?
홈페이지가 아닌 특정 콘텐츠(페이지)로 바로 연결되는 링크를 딥링크라고 합니다. 예를 들어보겠습니다. 안드로이드 개발 문서의 홈페이지는 아래의 주소입니다.

```
https://developer.android.com
```

하지만 제가 코틀린 스타일 가이드에대한 페이지로 바로 이동하는 링크를 걸고 싶다면 아래의 주소와 같이 해당 페이지로 바로 이동할 수 있는 딥링크를 사용하는 것입니다. 

```
https://developer.android.com/kotlin/style-guide
```

딥링크는 일반적으로 아래와 같이 `스키마(scheme)`와 `호스트(host) 및 경로(path)`의 두 부분으로 나뉘어져 있습니다. 

```
{scheme}://{host_path}
```

딥링크라는 개념은 모바일과 웹의 구분없이 사용되는 개념입니다. 다만 웹의 특성상 링크가 공개되어있어 대부분 딥링크를 사용하기 때문에 특별히 딥링크라고 부르지 않는 것같습니다. 웹처럼 링크가 공개되어있지 않은 모바일에서 이러한 딥링크를 구현하려면 어떻게 해야 할까요? 

또 하나의 문제가 있습니다. 모바일 앱이 이미 설치되어있는 경우에는 바로 해당 컨텐츠로 이동하겠지만, 아직 설치되지 않은 경우에는 어떻게 처리해야 할까요? 

이러한 문제를 해결하기 위해 나온 방법들을 아래에서 하나씩 살펴보겠습니다. 

## 모바일 딥링크의 종류
딥링크를 구글창에 검색해보면 앱링크, 다이나믹 링크 등 여러가지 관련 개념들이 많이 나옵니다. 한 번 제대로 짚고 넘어가지 않으면 계속 혼란스러울 것 같아 이번 기회에 각 방법들의 정확한 개념과 동작 방식, 그리고 문제점에 대해 정리해보려 합니다. 

지금부터 언급하는 것들은 모두 <I>**딥링크라는 개념을 구현하기 위한 방식**</I>들입니다. 즉, 모두 딥링크에 속하는 개념입니다. 

### 1. Traditional deep links
초기의 딥링크에서는 앱이 설치되어 있으면 딥링크를 통해 해당 컨텐츠로 정상적으로 이동할 수 있었습니다. 하지만 앱이 설치되어있지 않은 경우에는 에러 페이지나 대체 페이지를 보여줘야만 했습니다. 

![traditional-deep-link.png](../resources/images/traditional-deep-link.png)

이러한 문제를 해결하기 위해 나온 것이 아래의 deferred deep links입니다. 

### 2. Deferred deep links(지연된 딥링크)
deferred(지연된) 딥링크는 앱을 다운받지 않은 사용자에게도 해당 링크의 컨텐츠로 바로 이동할 수 있도록 해줍니다. 웹에서 딥링크를 클릭했을 때 이미 앱을 다운받은 사용자의 경우에는 기존의 딥링크와 동일하게 바로 해당 컨텐츠로 이동합니다. 

만약 앱이 설치되지 않은 경우라면 구글 플레이스토어나 앱스토어로 리다이렉트하여 이동한 뒤, 앱 다운로드가 완료되면 바로 해당 컨텐츠로 이동할 수 있도록 합니다. 위에서 언급했던 초기의 딥링크의 문제점을 개선한 것이라고 볼 수 있습니다. 

![deferred-deep-links.png](../resources/images/deferred-deep-links.png)

<!-- deferred deep links의 문제점 추가!!!!!-->

### 3. Android에서의 Deep Links와 App Links
저는 지금 안드로이드 개발을 공부하고 있기때문에 여기서는 안드로이드 관련 딥링크에서만 설명하겠습니다. ios에서는 [universal links(유니버셜 링크)](https://developer.apple.com/ios/universal-links/)라는 것을 지원하는 것 같습니다. 

안드로이드 공식 문서를 보면 `Deep Links(딥링크)`라는 것이 있고 `App Links(앱링크)`라는 것이 있습니다. 친절하게도 [이 두가지의 차이점에 대한 내용이 공식 문서에 있어](https://developer.android.com/training/app-links/verify-site-associations.html?hl=en#the-difference) 설명해보려합니다. 

안드로이드에서의 `Deep Links(딥링크)`는 사용자가 안드로이드앱에서 특정 활동을 직접 지정할 수 있도록 하는 [intent filter(인텐트 필터)](https://developer.android.com/guide/components/intents-filters?hl=ko)입니다. 딥링크를 사용하면 사용자가 링크를 클릭했을 때 disambiguation dialog(명확성 대화상자라는 번역이 어색한 것 같아 영어로 쓰겠습니다)가 열리고, 사용자가 지정된 URL을 처리할 수 있는 여러 앱 중 하나를 선택할 수 있습니다. 

예를 들어 아래의 그림1은 사용자가 지도 링크를 클릭한 후 지도앱 또는 크롬에서 링크를 열 것인지 묻는 disambiguation dialog를 보여줍니다. 

![그림1](../resources/images/disambiguation-dialog.png)

[그림1. The disambiguation dialog](https://developer.android.com/training/app-links/images/app-disambiguation_2x.png)

`App Links(앱링크)`는 웹 사이트의 URL을 기반으로 하는 딥링크입니다. 따라서 앱링크 방식으로 된 딥링크를 클릭하면 disambiguation dialog가 나타나는 것이 아니라 앱이 즉시 열립니다. 만약 앱이 설치되어있지 않다면 해당 웹페이지로 이동합니다. 

[아래의 표](https://developer.android.com/training/app-links/verify-site-associations.html?hl=en#the-difference)에서 안드로이드에서의 딥링크와 앱링크 두 가지 방식의 차이점을 확인할 수 있습니다. 

| |Deep Links|App Links|
|-|----------|---------|
|Intent URL scheme|http, https, 커스텀 스키마|http와 https만 가능|
|Intent action|모두 가능|`android.intent.action.VIEW`만 가능|
|Intent category|모두 가능|`android.intent.category.BROWSABLE`과 `android.intent.category.DEFAULT`만 가능|
|Link verification|필요없음|[Digital Asset Links](https://developers.google.com/digital-asset-links/v1/getting-started) 필요|
|User experience|사용자가 어떤 앱을 사용할 것인지 disambiguation dialog에서 선택할 수 있음|dialog없음. 해당 앱이 바로 열리거나 웹사이트로 이동함|
|Compatibility|모든 안드로이드 버전에서 가능|안드로이드 6.0 이상에서 가능|

### 4. Dynamic Links(Firebase)
구글 파이어베이스에서도 다이나믹 링크(dynamic links)라는 서비스로 딥링크를 지원해줍니다. 

안드로이드나 ios에서 직접 딥링크를 구현할떄와 파이어베이스의 다이나믹 링크를 사용할 때 어떤 점이 다른지 비교해보았습니다. 

|deep links 직접 구현|Firebase dynamic links|
|------------------|----------------------|
|안드로이드/ios 링크를 따로 만들어야함|안드로이드/ios를 하나의 링크로 사용 가능|
|사용자에게 보이는 링크가 2개|사용자에게 보이는 링크가 1개|
|앱 설치여부를 확인하는 기능을 개발자가 직접 구현해야함|파이어베이스에서 해줌|
|개발자만이 링크를 생성할 수 있음|마케터나 기획자가 원하는 방식으로 직접 할 수 있음|

지금까지 정리한바로는 다이나믹 링크가 가장 편리하고 유용해보입니다. 딥링크는 주로 외부에서 앱으로의 유입을 유도하기 위해 사용합니다. ~~때문에 광고를 집행하거나 이벤트 등을 하여 앱으로 들어오는 것을 유도할 때 많이 사용하는것 같은데, 링크를 매번 개발자가 생성하는 것이 아니라 기획자나 마케터가 직접 쉽게 생성할 수 있다는 것은 큰 장점이라고 생각합니다. 또한 파이어베이스에서 사용자의 유입을 추적하기에도 용이하지 않을까라는 생각이 듭니다. 제가 아직 사용해본적이 없기에 틀린 부분이 있다면 피드백 주시면 감사하겠습니다.~~ 다이나믹 링크의 단점 혹은 문제점이 무엇인지 궁금하여 구글링을 해보았지만 제가 못찾는건지 찾을 수가 없었습니다.*혹시 아시는 분은 알려주시면 감사하겠습니다.*

다음 글에서는 파이어베이스 다이나믹 링크로 딥링크를 직접 구현해보며 다이나믹 링크의 특징에 대해 알아보겠습니다.

### 참고 자료
- [안드로이드 공식 문서](https://developer.android.com/)
- [Google Ads - Deep link](https://support.google.com/google-ads/answer/6046977?hl=ko)
- [Firebase - Dynamic Liks](https://firebase.google.com/docs/dynamic-links/)
- [NAVER Developers - 네이버 앱 URL Scheme 연동](https://developers.naver.com/docs/utils/mobileapp/)
- [LINE Engineering - TRACKIT에서 딥링크를 사용하는 방법](https://engineering.linecorp.com/ko/blog/how-to-use-deeplink-in-trackit/)
- [branch blog - what is deep linking](https://branch.io/what-is-deep-linking/)
- [branch blog - 유니버설 링크, URI 스킴, 앱 링크 및 딥 링크: 무슨 차이가 있을까요?](https://blog.branch.io/ko/%EC%9C%A0%EB%8B%88%EB%B2%84%EC%84%A4-%EB%A7%81%ED%81%AC-uri-%EC%8A%A4%ED%82%B4-%EC%95%B1-%EB%A7%81%ED%81%AC-%EB%B0%8F-%EB%94%A5-%EB%A7%81%ED%81%AC-%EB%AC%B4%EC%8A%A8-%EC%B0%A8%EC%9D%B4%EA%B0%80/)
- [Stackoverflow - Android Deep Links and App Links confused](https://stackoverflow.com/questions/46169025/android-deep-links-and-app-links-confused)
- [박상권의 삽질블로그 - Firebase Dynamic Link로 사용자 유입시키기](https://gun0912.tistory.com/78)
