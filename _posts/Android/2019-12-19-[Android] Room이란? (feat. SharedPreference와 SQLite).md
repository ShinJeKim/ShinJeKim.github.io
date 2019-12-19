# [Android] Room이란? (feat. SharedPreference와 SQLite)
안드로이드의 Room을 공부하려 찾아보니 SQLite를 포함하는 개념이라는 것을 알게 되었다. 그래서 내친김에 안드로이드에서 일반적으로 데이터를 저장하는 방식을 모두 정리하고 비교해보려 한다.

## 1. SharedPreferences
Geeks for Geeks에 친절한 설명이 나와있었다.

> SharedPreferences는 적은 양의 원시 데이터(primitive data; ex. String, int, float, Boolean)를 키(key)/값(value)의 쌍(pair)의 형태로 기기의 저장공간에있는 앱 안에 XML 파일로 저장하는 방식을 의미한다. 예를 들면 "username"이라는 키에 username의 값을 저장하는 방식이다. 그리고 "username"이라는 키로 다시 해당 값을 찾을 수 있다. 그리고 preference를 저장하고 필요할 때 언제든지 가져 오는 데 사용할 수있는 간단한 Shared Preferences API를 사용할 수 있습니다. Shared Preferences 클래스는 API가 데이터를 읽고 쓰고 관리할 수 있는 기능을 제공합니다.
> 
> Shared Preferences는 각기 다른 상황에 사용될 수 있습니다. 예를 들어, 사용자 설정을 저장하거나 앱 내에서 다양한 활동에 사용할 수있는 데이터를 저장해야 할 때 사용할 수 있습니다. 아시다시피 onPause()는 액티비티가 백그라운드에 놓이기 전이나 destroy되기 전에 항상 호출되기 때문에, 데이터를 지속적으로 저장하려면 onPause()에 저장하는 것이 좋습니다. 그리고 이 데이터는 액티비티의 onCreate()에서 다시 저장될 수 있습니다. SharedPreferences를 사용하여 저장된 데이터는 애플리케이션 내에서 private하게 유지됩니다. 하지만 Shared Preferences는 액티비티의 instance state와는 다릅니다. 

그러니까 Shared Preferences는 소량의 데이터 ~~*(소량이라는건 어느 정도까지일까..? 찾아보자)*~~ 를 키와 값의 쌍으로 기기에 파일로 저장할 수 있게 해주는 것이다. 그렇다면, Shared Preferences와 액티비티에서 자주 사용하던 Saved Instance State는 어떻게 다를까? Geeks for Geeks에 이 두 개념을 비교한 표도 친절하게 나와있다. 이렇게 비교해보니 명확하게 이해가 된다. Shared Preferences는 세션과 관계없이 영속적으로 보관해야하는 데이터가 있을 때 사용하고, Saved Instance State는 유저의 세선 내에서 액티비티의 instance를 저장하는 데에 사용하는 것이다.

| Shared Preferences                                                | Saved Instance State                            |
| ----------------------------------------------------------------- | ----------------------------------------------- |
| 유저 세션이 지나도 데이터가 영속적으로 보관됨(앱이 죽거나 다시 시작되는 경우, 혹은 기기가 다시 시작되는 경우에도) | 동일한 유저 세선 내에서 액티비티 인스턴스를 영속적으로 저장함              |
| 유저의 설정이나 게임 스코어와 같이 세션이 지나도 보존되어있어야 하는 데이터                        | 선택된 탭이나 액티비티의 현재 상태와 같이 세션이 끝났을 때에 보존되면 안되는 데이터 |
| 일반적으로 유저의 preference를 저장하는 데에 사용됨                                 | 일반적으로 기기가 회전된 후 상태를 다시 되돌리는 데에(recreate) 사용됨    |


## 2. SQLite
[SQLite 공식 웹페이지](https://sqlite.org/index.html)에 가보면 "What is SQLite?"라는 설명이 있다. 발췌하여 번역해보았다.

> SQLite란 무엇인가?
> 
> SQLite는 C언어로 만들어진 라이브러리이며, [작고(small)](https://sqlite.org/footprint.html), [빠르고(fast)](https://sqlite.org/fasterthanfs.html), [자가수용적이고(self-contained)](https://sqlite.org/selfcontained.html), [고의존적이며(high-reliability)](https://sqlite.org/hirely.html), [모든 특징을 가지고 있는(full-featured)](https://sqlite.org/fullsql.html) SQL 데이터베이스 엔진입니다. SQLite는 전 세계에서 [가장 많이 쓰이는](https://sqlite.org/mostdeployed.html) 데이터베이스 엔진입니다. SQLite는 모든 모바일 폰과 컴퓨터 내부에 심어져있으며, 사람들이 매일 사용하는 수많은 애플리케이션의 내부에 패키지화되어(bundled inside) 있습니다. [더 많은 정보는 이곳을 클릭해주세요.](https://sqlite.org/about.html)
>
> SQLite [파일 형식](https://sqlite.org/fileformat2.html)은 안정적이고, 크로스 플랫폼을 지원하고, 과거의 기술들과 호환이 가능(backwards compatible)하며 개발자들이 최소한 2050년까지는 유지할 것을 선언했습니다. SQLite 데이터베이스 파일들은 일반적으로 풍부한 컨텐트를 시스템간에( [[1]](https://sqlite.org/aff_short.html) [[2]](https://sqlite.org/sqlar.html) [[3]](https://sqlite.org/appfileformat.html) ) 전송하기 위한 컨테이너로 사용되며, 장기간동안 데이터를 저장하기 위한 형식으로 사용됩니다( [[4]](https://sqlite.org/locrsf.html) ). 1조개가 넘는 SQLite 데이터베이스가 현재 사용중입니다( [[5]](https://sqlite.org/mostdeployed.html) ).
> 
> SQLite의 [소스코드](https://sqlite.org/src)는 [public-domain](https://sqlite.org/copyright.html)이고 모두에게 어떤 목적으로든 열려있습니다.

~~*무언가 굉장한..! 데이터베이스 엔진이라고 하는 것 같다.*~~ 그렇다면 MySQL, Oracle 등의 다른 데이터베이스 엔진들과의 차이점은 무엇일까? 하는 궁금증이 생겼다. [공식 웹사이트](https://sqlite.org/whentouse.html)를 보니 친절하게도 이에 대한 설명이 나와있다. 

> SQLite는 다른 종류의 문제를 해결하기 위한 데이터베이스 엔진이기때문에, MySQL/Oracle/PostgreSQL/SQL 등과 같은 다른 클라이언트/서버 데이터베이스 엔진과 직접적으로 비교할 수 없습니다.
>  
> 클라이언트/서버 SQL 데이터베이스 엔진은 공유된 엔터프라이즈 데이터의 저장소를 구현하는 것을 목적으로 합니다. 클라이언트/서버 SQL 데이터베이스 엔진은 확장성(scalability), 동시성(concurrency), 중앙집중화(centralization), 그리고 통제성(control)을 강조합니다. SQLite는 개별 애플리케이션과 개별 기기에 로컬 데이터 스토리지를 제공하기 위해 노력합니다. SQLite는 경제성(economy), 효율성(efficiency), 신뢰성(reliability), 독립성(independence), 그리고 간결함(simplicity)을 강조합니다.
> 
> SQLite는 클라이언트/서버 데이터베이스와 경쟁하지 않습니다. SQLite는 [fopen()](http://man.he.net/man3/fopen)과 경쟁합니다. ~~*(찾아보니 fopen()은 C 언어의 파일 입출력 함수라는데 엄청 빠르다는 것을 강조하는 말 같기도 하다.)*~~

즉 요약하자면 SQLite는 파일 형식으로 데이터를 저장하며, 소규모/로컬/빠른/간결한 데이터 저장을 목적으로 하는 관계형 데이터베이스 엔진이다. SQLite의 사용이 권장되는 곳은 임베디드 기기, IoT, 응용 프로그램 파일 형식, 웹사이트(트래픽이 적거나 중간 정도인), 엔터프라이즈 데이터용 캐시 등이다.

이제 SQLite가 모바일 앱에서 사용하기 좋은 관계형 데이터베이스라는 것은 이해했다. 그렇다면 Room은 무엇일까? 그리고 왜 필요할까?

## 3. Room
[안드로이드 공식 문서](https://developer.android.com/training/data-storage/room/)를 보니 SQLite 대신 Room이 왜 필요한지에 대한 설명이 나와있다.

> Room은 SQLite에 대한 추상화 레이어를 제공하여 원활한 데이터베이스 액세스를 지원하는 동시에 SQLite를 완벽히 활용합니다. 
> 
> 상당한 양의 구조화된 데이터를 처리하는 애플리케이션은 데이터를 로컬로 유지하여 큰 이점을 얻을 수 있습니다. 가장 일반적인 사용 사례는 관련 데이터를 캐싱하는 것입니다. 기기가 네트워크에 액세스할 수 없을 때, 이러한 방식으로 오프라인 상태인 동안에도 사용자가 여전히 콘텐츠를 탐색할 수 있습니다. 나중에 기기가 다시 온라인 상태가 되면 사용자가 시작한 콘텐츠 변경사항이 서버에 동기화됩니다. 
> 
> Room이 사용자를 대신하여 이러한 문제를 처리하므로 SQLite 대신 Room을 사용하는 것이 **좋습니다.**

(+ Room 설명 추가 예정)

### 참고 자료
- [Geeks for Geeks - Shared Preferences in Android with Examples](https://www.geeksforgeeks.org/shared-preferences-in-android-with-examples/)
- [Room으로 DB 저장하기](https://blog.yena.io/studynote/2018/09/08/Android-Kotlin-Room.html)
