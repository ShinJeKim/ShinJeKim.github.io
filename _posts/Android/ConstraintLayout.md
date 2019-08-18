# **ConstraintLayout**

> 제약 레이아웃은 android.view.ViewGroup에 속한 레이아웃이며 위젯의 위치(position)와 크기(size)를 지정할 수 있게 해줍니다.

현재 사용할 수 있는 제약(constraints)은 아래와 같습니다.

- [Relative positioning](#relative-positioning)
- [Margins](#margins)
- [Centering positioning](#centering-positioning-and-bias)
- [Circular positioning](#circular-positioning)
- [Visibility behavior](#visibility-behavior)
- [Dimension constraints](#dimension-constraints)
- [Chains](#chains)
- [Virtual Helpers objects](#virtual-helpers-objects)
- [Optimizer](#optimizer)

위의 개념들을 차례대로 살펴보겠습니다. 아래의 설명에서 대상 뷰/위젯이란 제약을 지정하는 뷰/위젯을 의미합니다. 

## Relative positioning
Relative positioning(상대 위치 지정)은 제약 레이아웃에서 가장 기초적인 개념입니다. 대상 위젯의 위치를 지정할 때, 다른 위젯으로부터의 상대적인 위치에 지정하는 것입니다. 예를 들면 아래 그림처럼 버튼B라는 위젯의 위치를 지정하기 위해서 버튼A의 오른쪽으로 버튼B를 포지셔닝 할 수 있습니다.

![relative-positioning-example](../resources/images/relative-positioning-example.png)

위의 레이아웃의 코드는 아래와 같습니다. 다른 부분은 나중에 차차 살펴보고 일단 `app:layout_constraint...`라고 되어있는 부분만 살펴보도록 하겠습니다. `app:layout_constraint...`라고 되어 있는 부분을 보면 모두 `app:layout_constraint... = "parent"` 혹은 `app:layout_constraint... = "@+id/아이디"`로 parent 혹은 id가 할당되어 있는 것을 보실 수 있습니다. 이처럼 Relative positioning을 하기 위해서는 기준으로 잡을 수 있는 parent나 다른 위젯의 id가 항상 필요합니다. 

이 예시에서는 버튼B의 시작점(start)을 버튼A의 끝(end)에 연결하였습니다. 이는 버튼B 코드의 `app:layout_constraintStart_toEndOf="@+id/buttonA`에 해당됩니다. 위젯을 화면에 띄우기 위해서는 상하좌우의 위치 제약(constraint)을 모두 지정해주어야 하는데, 이 예제에서는 모두 임의로 parent를 기준으로 하였습니다.
```xml
<Button
    android:id="@+id/buttonA"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginStart="80dp"
    android:layout_marginLeft="80dp"
    android:layout_marginTop="328dp"
    android:text="버튼A"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

<Button
    android:id="@+id/buttonB"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginStart="72dp"
    android:layout_marginLeft="72dp"
    android:layout_marginTop="328dp"
    android:text="버튼B"
    app:layout_constraintStart_toEndOf="@+id/buttonA"
    app:layout_constraintTop_toTopOf="parent" />
```

상대 위치 지정은 아래와 같이 수직/수평의 모든 방향으로 지정할 수 있습니다.

- Horizontal Axis(수평): left, right, start and end sides(좌, 우, 시작과 끝면)
- Vertical Axis(수직): top, bottom sides and text baseline(위, 아래, 텍스트 베이스라인)

![relative-positioning-constraints](https://developer.android.com/reference/android/support/constraint/resources/images/relative-positioning-constraints.png)

Horizontal Axis를 기준으로 위치를 지정할 때 아래의 1번과 2번 코드 모두 동일한 결과를 만들어냅니다. 어떤 것을 사용하는 것이 좋을까요?
``` xml
1번
<Button ...
    app:layout_constraintLeft_toRightOf="@+id/buttonA" />
```
``` xml
2번
<Button ...
    app:layout_constraintStart_toEndOf="@+id/buttonA" />
```
2번 코드처럼 start와 end를 사용하는 것이 권장된다고 합니다. 많은 국가는 LTR(Left to Right) 언어, 즉 왼쪽에서 오른쪽 방향으로 쓰고 읽는 언어를 사용하지만 아랍 등의 국가에서는 RTL(Right to Left) 언어를 사용합니다. 따라서 레이아웃 리소스가 LTR과 RTL을 모두 지원할 수 있도록 left/right 대신 start/end를 사용하는 것이 바람직하다고 합니다.

Relative positioning에서 사용 가능한 제약은 아래와 같습니다.
```
- layout_constraintLeft_toLeftOf
- layout_constraintLeft_toRightOf
- layout_constraintRight_toLeftOf
- layout_constraintRight_toRightOf
- layout_constraintTop_toTopOf
- layout_constraintTop_toBottomOf
- layout_constraintBottom_toTopOf
- layout_constraintBottom_toBottomOf
- layout_constraintBaseline_toBaselineOf
- layout_constraintStart_toEndOf
- layout_constraintStart_toStartOf
- layout_constraintEnd_toStartOf
- layout_constraintEnd_toEndOf
```

## Margins
마진(margin)은 대상 위젯의 테두리 바깥의 여백 면적을 의미하며, 0이상으로만 지정될 수 있습니다. 마진의 크기를 지정하면 기존에 지정되어있던 제약(constraint)에 따라 기준면(source side)과 마진이 지정된 위젯 사이에 공간이 생깁니다. [Relative positioning](#relative-positioning)부분의 그림을 보시면 버튼A와 버튼B사이에 72dp만큼의 간격이 있습니다. 이는 코드에서 아래 부분에 해당합니다. 

```xml
<Button ...
    android:layout_marginStart="72dp"
    android:layout_marginLeft="72dp"
    android:layout_marginTop="328dp" />
```
하나씩 살펴보면, 버튼B의 `Start`는 버튼A로부터 72dp만큼의 `margin`이 있습니다. 아래의 코드도 사실은 같은 의미로, 버튼B의 `Left`는 버튼A로부터 72dp만큼의 `margin`을 지정한 것입니다. RTL 지원을 생각하면 marginStart만 사용하는 것이 바람직하지만 API17 이전의 버전을 지원하기 위해서는 marginStart/marginEnd를 사용할 때에 marginLeft/Right도 같이 입력해야 에러가 발생하지 않습니다. 마지막으로, 버튼B의 `Top`은 parent로부터 328dp만큼의 `margin`이 있습니다. 

margins에서 사용 가능한 제약은 아래와 같습니다.
```
- android:layout_marginStart
- android:layout_marginEnd
- android:layout_marginLeft
- android:layout_marginTop
- android:layout_marginRight
- android:layout_marginBottom
```

#### Margins when connected to a GONE widget
대상 위젯이 View.GONE에 속한 GONE 위젯이라면 기본 마진과는 다른 gone 속성을 사용한 마진을 지정할 수 있습니다. 
GONE 위젯에 사용 가능한 제약은 아래와 같습니다.
```
- layout_goneMarginStart
- layout_goneMarginEnd
- layout_goneMarginLeft
- layout_goneMarginTop
- layout_goneMarginRight
- layout_goneMarginBottom
```
gone margin에 대한 설명은 GONE 위젯에 대한 설명이 전제되어야하므로 아래의 [Visibility behavior](#visibility-behavior) 부분에서 설명하도록 하겠습니다. 

## Centering positioning and bias
ConstraintLayout의 장점은 불가능해보이는 제약을 지정했을 때 알맞게 조절해준다는 것입니다. 우선 아래의 예시 코드를 보겠습니다.
```xml
<Button ...
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent" />
```
위의 코드같은 경우에는 버튼의 너비가 상위 레이아웃인 ConstraintLayout의 너비와 일치해야만 가능합니다. 이럴 때 안드로이드에서는 아래와 같이 대상 위젯을 parent의 정중앙에 위치시킵니다. 이는 수평/수직 제약 모두 해당됩니다. 

![centerint-positioning.png](../resources/images/centering-positioning-example.png)

#### Bias
대상 위젯의 양 측으로 제약을 줄 때 기본적으로는 정중앙에 위치하도록 포지셔닝됩니다. 이를 변경하고 싶다면 bias 속성을 사용하여 다르게 포지셔닝을 할 수 있습니다. bias를 사용하면 스크린의 크기 변경에 유연하게 대응할 수 있습니다. 
```
- layout_constraintHorizontal_bias
- layout_constraintVertical_bias
```

![bias-example.png](../resources/images/bias-example.png)

예를 들어 위의 그림처럼 왼쪽으로 치우치게 포지셔닝을 하고 싶다면 layout_constraintHorizontal_bias를 0.5이하로 설정하면 됩니다. 저는 아래 코드와 같이 0.2로 설정해보았습니다. 
```xml
<Button ...
    app:layout_constraintHorizontal_bias="0.2" />
```

## Circular Positioning
Circular Positioning(원형 위치 지정)이란 대상 위젯의 중간점으로부터 다른 위젯의 중간점까지의 각도와 거리를 지정하여 제약을 주는 것을 의미합니다. 즉 아래의 그림처럼 위젯의 중간점을 중심으로 원을 그렸을 때, 반지름의 길이만큼 떨어진 원 둘레의 한 지점에 다른 위젯을 포지셔닝 하는 것을 말합니다. 
```
- layout_constraintCircle : 다른 위젯의 id를 지정합니다.
- layout_constraintCircleRadius : 대상 위젯에서 다른 위젯의 중간점까지의 거리를 지정합니다. 즉 대상 위젯을 둘러싼 원의 반지름을 지정하는 것을 의미합니다. 
- layout_constraintCircleAngle : 다른 위젯이 어떤 각도에 위치할지를 지정합니다.(범위: 0~360도)
```
아래의 왼쪽과 같이 버튼B의 제약을 지정하면, 오른쪽의 그림과 같은 결과가 나옵니다. 

![circular-positioning](../resources/images/circular-positioning.png)

코드로 자세히 보겠습니다. 버튼A의 id인 buttonA를 constraintCircle로 지정하였습니다. 각도는 60도로 하였고, 버튼A의 중점으로부터 100dp만큼의 거리에 버튼B의 중점이 위치하도록 하였습니다. 
```xml
<Button ...
    app:layout_constraintCircle="@id/buttonA"
    app:layout_constraintCircleAngle="60"
    app:layout_constraintCircleRadius="100dp" />
```

## Visibility behavior
View.GONE에 속한 GONE 위젯은 뷰에서 보여지지 않으며 자리를 차지하지도 않습니다. 하지만 대상 위젯에 적용된 면적은 그대로 유지됩니다. 이렇게만 보면 무슨 말인지 이해가 가지 않습니다. 지금부터 이것이 어떤 의미인지 찬찬히 설명해보겠습니다. 

우선, GONE 위젯은 다음과 같은 속성을 가집니다.
```
- 레이아웃에서 GONE 위젯의 면적은 0으로 간주됩니다.
- 만약 GONE 위젯이 다른 위젯과 연결된 제약을 가지고 있다면 그 제약은 유지되지만, GONE 위젯의 마진은 0으로 유지됩니다. 
```

예를 들어 보겠습니다. 아래 왼쪽의 그림에서 버튼B는 버튼A의 end를 기준으로 80dp의 마진을 두고 있습니다. 그리고 버튼A는 parent의 start를 기준으로 70dp의 마진을 두고 있습니다. 여기서 버튼A의 visibility를 gone으로 처리하면 버튼A의 면적과 마진이 모두 0으로 간주됩니다. 따라서 오른쪽 그림처럼 버튼A는 보이지 않게 되고 버튼B만 보이게 되며, 버튼 A가 없어진 면적만큼 버튼B의 위치가 이동한 것을 보실 수 있습니다.  

![gone.png](../resources/images/bias-example.png)

이 때의 코드는 아래와 같습니다. 코드를 살펴보면, 버튼B에 걸려있는 버튼A에 대한 제약과 속성은 그대로 남아있는 것을 확인할 수 있습니다. 그리고 버튼A에 지정된 제약과 속성 또한 그대로 유지되어있습니다. 하지만 버튼A의 `visibility`를 `gone`으로 지정했기때문에 레이아웃에서 버튼A가 차지하는 면적과 마진은 0으로 간주되고, 그래서 위의 그림과 같은 뷰를 만들 수 있게 되는 것입니다. 
```xml
<Button
    android:id="@+id/buttonA"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginStart="70dp"
    android:layout_marginLeft="70dp"
    android:layout_marginTop="320dp"
    android:text="버튼A"
    android:visibility="gone"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

<Button
    android:id="@+id/buttonB"
    ...
    android:text="버튼B"
    app:layout_constraintStart_toEndOf="@+id/buttonA"
    app:layout_constraintTop_toTopOf="parent" />
```
이러한 GONE 제약은 일시적으로 위젯을 사라지게 하고싶지만 레이아웃은 그대로 유지해야 할 때 유용하게 사용할 수 있습니다. 
그런데 여기서 하나의 문제가 발생합니다. 버튼A를 GONE 위젯으로 만들어버리면 버튼B의 위치가 달라집니다. 버튼B의 위치도 그대로 유지하고싶다면 어떻게 해야 할까요? 이 떄 사용하는 것이 [위에서 언급한](#margins-when-connected-to-a-GONE-widget) `gone margin`입니다. 

## Dimensions constraints
아래는 dimension과 관련된 제약들입니다. dimension은 치수/면적/크기 등으로 번역될 수 있는데, 문맥에 맞게 혼용하여 사용하도록 하겠습니다. 

#### Minimum dimensions on ConstraintLayout
아래의 제약을 사용하여 제약 레이아웃의 최솟값과 최대값을 지정할 수 있습니다. 
```
- android:minWidth : 레이아웃의 최소 너비를 지정합니다
- android:minHeight : 레이아웃의 최소 높이를 지정합니다
- android:maxWidth : 레이아웃의 최대 너비를 지정합니다
- android:maxHeight : 레이아웃의 최대 높이를 지정합니다
```
제약 레이아웃의 크기가 `WRAP_CONTENT`로 지정되어있을 때 위의 속성을 사용할 수 있습니다.

#### Widgets dimension constraints
위젯의 크기는 아래와 같이 `android:layout_width`, `android:layout_height`의 속성을 사용하여 3가지의 방법으로 지정할 수 있습니다. 
```
1. 특정 크기로 지정하는 방법
2. WRAP_CONTENT를 사용하는 방법
3. MATCH_CONSTRAINT를 사용하는 방법(width/height를 0dp로 지정하는 것을 의미합니다)
```
1번 방법은 말 그대로 대상 위젯의 width와 height의 크기를 직접 지정하거나 Dimension 리소스를 통해 지정하는 것을 의미합니다. 직접 크기를 지정할때에는 아래와 같이 할 수 있습니다. 
```xml
<!--직접 값을 입력하여 크기를 지정하는 방법-->
<Button ...
    android:layout_width="100dp"
    android:layout_height="100dp" />
```
그럼 Dimension 리소스를 통해 지정한다는 것은 무엇일까요? 안드로이드에서는 이미지나 문자열과 같은 애플리케이선의 리소스를 독립적으로 관리할 수 있도록 지원합니다. dimension과 관련된 속성의 경우 `YourProject/res/values/dimens.xml` 경로의 파일에서 관리할 수 있습니다.(이 경로는 규칙이므로 꼭 지켜주셔야 적용이 됩니다.) 리소스에 관한 것이 더 궁금하시다면 [안드로이드 공식문서](https://developer.android.com/guide/topics/resources/providing-resources?hl=ko#QualifierRules)에서 확인하실 수 있습니다. 

**주의**: `MATCH_PARENT` 속성은 제약 레이아웃에 속한 위젯에는 사용하지 않는 것이 권장됩니다. 대신 `parent`에 `left/right` 혹은 `top/bottom` 제약을 지정하여 `MATCH_CONSTRAINT` 속성을 사용할 수 있습니다.  

#### WRAP_CONTENT : enforcing constraints
`WRAP_CONTENT`는 대상 위젯 내부의 컨텐츠 크기에 위젯의 크기를 자동으로 맞춥니다. 이 때 아래의 속성을 사용하여 대상 위젯의 크기에 제한을 둘 수 있습니다.
```
- app:layout_constrainedWidth=”true|false”
- app:layout_constrainedHeight=”true|false”
```
아래의 예시를 한 번 보겠습니다. TextView의 width를 wrap_content로 지정해놓았습니다. 그런데 TextView의 content 길이가 너무 길어 TextView의 width가 길어졌고, 그로 인해 화면에서 Button이 보이지 않습니다. 이 때 TextView에 `app:layout_constrainedWidth=”true”` 속성을 추가하면 width에 제한을 주면 위젯 안의 content 길이가 길어지더라도 위와 같이 레이아웃이 유지됩니다. 

![constraint-width.png](../resources/images/constraint-width.png) 

#### MATCH_CONSTRAINT dimensions
위젯의 크기를 MATCH_CONSTRAINT로 설정하면 기본적으로 해당 위젯이 차지할 수 있는 모든 면적을 다 차지하게 됩니다. 이 때 아래의 속성을 사용하여 위젯의 크기를 변경할 수 있습니다. 
```
- layout_constraintWidth_min
- layout_constraintHeight_min
- layout_constraintWidth_max
- layout_constraintHeight_max
- layout_constraintWidth_percent
- layout_constraintHeight_percent
```

##### Min and Max
min과 max로 지정되는 값은 dp의 단위로 직접 크기를 지정할 수도 있고, WRAP_CONTENT와 동일한 결과를 내는 `wrap`을 사용하여 지정할 수도 있습니다.

##### Percent dimension
퍼센트를 사용하기 위해서는 아래의 설정이 필요합니다. 
```
- 대상 위젯의 크기(dimension)는 MATCH_CONSTRAINT(0dp)로 지정되어야 합니다.
- app:layout_constraintWidth_default="percent" 혹은
  app:layout_constraintHeight_default="percent"를 사용하여 기본값을 퍼센트로 설정하여야 합니다.
- layout_constraintWidth_percent 혹은 layout_constraintHeight_percent 속성을 0과 1 사이의 값으로 지정해야 합니다.
```

#### Ratio
위젯의 치수(dimension) 대한 비율을 이용하여 다른 부분의 치수를 지정할 수도 있습니다. 비율을 사용하기 위해서는 최소한 하나의 치수(dimension)를 0dp(MATCH_CONSTRAINT)로 지정해야만 합니다. 그리고 layout_constraintDimensionRatio를 사용하여 비율을 지정하면 됩니다. 비율을 입력하는 방식에는 아래와 같이 두 가지의 방식이 있습니다. 
```   
- app:layout_constraintDimensionRatio="1:1" (width:height로 표현하는 방법)
- app:layout_constraintDimensionRatio="1.0" (width와 height의 비율을 float값으로 표현하는 방법)
```

아래의 코드에서는 width를 wrap_content로 지정하고 height를 0dp로 지정한 뒤 1:1의 비율을 입력하였습니다. 결과로는 버튼의 content 크기에 맞춰진 정사각형 버튼이 만들어집니다. 

```xml
<Button ...
    android:layout_width="wrap_content"
    android:layout_height="0dp"
    app:layout_constraintDimensionRatio="1:1" />
```

width와 height이 모두 MATCH_CONSTRAINT(0dp)로 지정되어 있을 때에도 비율을 지정할 수 있습니다. 이 경우에는 아래와 같이 `W,` 혹은 `H,`를 비율 앞에 추가하여 어떤 쪽에 제약을 줄 것인지를 지정할 수 있습니다. **이 때 주의해야 할 점은 width와 height를 모두 0dp로 지정하는 것이기때문에 start/end, top/bottom에 제약이 걸려있어야 제대로 테스트할 수 있습니다.**

```
- app:layout_constraintDimensionRatio = "H, x:y" (width를 constraint에 맞춰 설정한 뒤 비율에 따라 높이를 결정합니다)
- app:layout_constraintDimensionRatio = "W, x:y" (height를 constraint에 맞춰 설정한 뒤 비율에 따라 높이를 결정합니다)
```

아래의 예시를 통해 조금 더 자세히 설명해보겠습니다. 

```xml
<Button ...
    android:layout_width="0dp"
    android:layout_height="0dp"
    app:layout_constraintDimensionRatio="W, 1:3" />
```

우선 width와 height를 모두 0dp로 지정하였습니다. 마지막 라인의 `app:layout_constraintDimensionRatio="W, 1:3"`의 의미는 height를 먼저 constraint에 맞춰 3으로 설정한 후, width를 1로 설정하겠다는 것입니다. 위의 코드를 입력하면 아래와 같은 결과가 나옵니다. (코드에서는 생략했지만 start/end, top/bottom 제약을 모두 parent로 설정하였습니다.)

![dimenstion-ratio-width](../resources/images/dimenstion-ratio-width.png)

그럼 아래와 같이 `H, 1:3`으로 지정하면 어떻게 될까요?
```xml
<Button ...
    android:layout_width="0dp"
    android:layout_height="0dp"
    app:layout_constraintDimensionRatio="H, 1:3" />
```

마지막 라인의 `app:layout_constraintDimensionRatio="H, 1:3"` 부분에 의해 width가 먼저 parent에 맞춰 1로 설정이 되고, 그 다음 height가 그에 대한 3의 비율로 설정됩니다. 따라서 아래 그림과 같이 위젯이 화면을 벗어나게됩니다. 

![dimenstion-ratio-height](../resources/images/dimenstion-ratio-height.png)

## Chains
체인을 사용하면 하나의 수평/수직 축(axis)을 기준으로 여러개의 위젯들을 그룹화하여 움직일 수 있습니다. 하나의 축을 기준으로 체인을 지정하더라도 다른 축에 대한 제약은 독립적으로 적용할 수 있습니다. 즉, 수평 체인을 지정하더라도 수직으로 별개의 제약을 지정할 수 있습니다. 

#### Creating a chain
아래와 같이 위젯들이 양방향으로 연결되어있는 것을 가장 기본적인 형태의 체인으로 볼 수 있습니다. 

![chain](../resources/images/horizontal-chain.png)

#### Chain heads
체인은 가장 첫 번째에 있는 요소인 헤드(head)의 속성에 의해 제어됩니다. 수평 체인에서는 가장 왼쪽에 있는 위젯이 헤드가 되고, 수직 체인에서는 가장 위쪽에 있는 위젯이 헤드가 됩니다. 

![chain](../resources/images/chain-head.png)

#### Margins in chains
체인 연결에 마진을 지정할수도 있습니다. `spread chain`의 경우 할당된 공간에서 마진의 크기만큼 여백이 줄어듭니다. 아래에서 자세히 설명해보겠습니다. 

#### Chain Style
체인에 스타일을 주고 싶을 때에는 체인의 첫번째 요소를 `layout_constraintHorizontal_chainStyle` 혹은 `layout_constraintVertical_chainStyle`의 속성으로 지정하면 됩니다. 디폴트값은 `CHAIN_SPREAD`입니다.
```
- CHAIN_SPREAD : 기본 스타일입니다. 빈 공간이 양측으로 균등하게 분산됩니다. 
- Weighted chain : 각 요소들의 가중치에 따라 추가 공간을 확보하게 됩니다. layout_constraintHorizontal_weight 혹은 layout_constraintVertical_weight 속성을 이용하여 지정할 수 있습니다. 
- CHAIN_SPREAD_INSIDE : 체인의 안쪽으로만 간격을 두어 분산시킵니다. 
- CHAIN_PACKED : 체인의 각 요소들을 한 곳에 묶습니다. 자식 요소의 수직/수평 bias 속성은 CHAIN_PACKED로 묶인 요소들의 위치에 영향을 주게됩니다. 
```

![chains-styles](https://developer.android.com/reference/android/support/constraint/resources/images/chains-styles.png)

#### Margins and chains
체인에서 마진은 합산되는 성질이 있습니다. 예를 들어, 수평 체인에서 한 요소의 오른쪽 마진을 10dp로 설정하고, 그 다음 요소의 왼쪽 마진을 5dp로 설정하면 두 요소간의 마진은 총 15dp가 됩니다. 체인의 요소들을 포지셔닝하기 위해 남은 공간을 계산할 때, 요소와 그 요소의 마진이 차지하는 공간을 합산하여 하나로 간주합니다. 

## Virtual Helper objects
앞서 설명했던 내장 함수 이외에도 ConstraintLayout에 포함된 helper를 이용하여 레이아웃을 조정할 수 있습니다. `Guideline` 객체를 사용하면 ConstraintLayout을 기준으로 하는 수평/수직의 가이드라인을 만들 수 있으며, `Barrier`와 `Group` 속성도 사용할 수 있습니다. 가이드라인(Guideline)과 배리어(Barrier)는 렌더링되어 뷰에 나타나는 요소는 아닙니다.(View.GONE의 gone 뷰로 표시됩니다) 주로 가이드라인에 위젯을 붙여 위젯의 위치를 쉽게 변경하기 위해 사용합니다. 

#### Guideline
`가이드라인(Guideline)`은 수평/수직 모두 적용할 수 있습니다. 수평 가이드라인(horizontal guidelines)의 height는 0이고 width는 parent인 ConstraintLayout에 맞춰집니다. 수직 가이드라인(vertical guidelines)의 width는 0이고 height는 parent인 ConstraintLayout에 맞춰집니다.

가이드라인의 위치를 지정하기 위해서 아래의 세 가지 방법을 사용할 수 있습니다.
```
- layout_constraintGuide_begin : 레이아웃의 left 또는 top에서부터의 고정 거리를 지정합니다.
- layout_constraintGuide_end : 레이아웃의 right 혹은 bottom에서부터의 고정 거리를 지정합니다.
- layout_constraintGuide_percent : 레이아웃에서의 width 혹은 height의 퍼센트를 지정합니다. 
```

#### Barrier
`배리어(Barrier)`는 복수의 위젯을 참조하여 가상의 장벽(일종의 가이드라인)을 만듭니다. 배리어 제약이 지정된 위젯들은 위치가 변경될 때 배리어에 맞게 변경됩니다. 이 때 배리어와 가장 가까이 있는 위젯(공식문서에서는 "the most extreme widget on the specified side"라고 표현하고 있습니다.)을 기준으로 장벽을 만듭니다. 아래의 그림 1번을 보시면 ButtonA가 배리어와 가장 가까이 있을 때에는 ButtonA를 기준으로 배리어가 형성됩니다. 하지만 2번 그림처럼 ButtonB가 배리어 방향의 가장 극단으로 옮겨지면 ButtonB를 기준으로 배리어가 형성됩니다.

![barrier.png](../resources/images/barrier.png)

#### Group
`그룹(Group)` 헬퍼를 통해 복수의 위젯을 그룹화하여 아래와 같이 visibility 속성(visible | invisible | gone)을 동시에 제어할 수 있습니다.
```xml
<androidx.constraintlayout.widget.Group
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" 
    android:visibility="invisible"
    app:constraint_referenced_ids="button, button2, button3" />
```

위 코드의 `android:visibility` 부분에서 visibility 속성을 지정할 수 있으며, `app:constraint_referenced_ids`에서 그룹으로 지정하고자 하는 위젯의 아이디를 입력할 수 있습니다.

## Optimizer
Constraint Layout 1.1버전부터는 레이아웃의 속도를 높이기 위해 최적화 방법을 제공합니다. ConstraintLayout 요소에 `app:layout_optimizationLevel` 태그를 추가하여 아래와 같이 최적화 레벨을 지정할 수 있습니다. 

```
- none : 최적화 사용 안함
- standard : 디폴트 레벨
- direct : 고정된 요소에 연결된 제약 조건에 관련된 최적화
- barrier : barrier 제약 조건 관련 최적화
- chain : 체인 제약 조건 최적화(아직 테스트중인 기능)
- dimensions : 크기(dimensions) 측정 최적화(아직 테스트중인 기능)
```

## 참고자료
- [Android Documentation](https://developer.android.com/reference/android/support/constraint/ConstraintLayout)
- [Official Android Developers Publication on Medium ](https://medium.com/androiddevelopers/introducing-constraint-layout-1-1-d07fc02406bc)
- [ConstraintLayout과 함께 안드로이드 앱 편하게 개발하기](https://academy.realm.io/kr/posts/cool-constraintlayout-droidcon-boston-2017/)
- [지금은 Constraint layout 시대](https://www.slideshare.net/kingori/constraint-layout-94663983)
- [ConstraintLayout.com](https://constraintlayout.com/basics/barriers.html)
