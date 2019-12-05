---
layout: post
title: "[Android] 프래그먼트에서 No view found for id... 에러가 날 때"
date: 2019-12-04
categories: Android
tags: Android
comments: true
---
No view found for id 0x7f07005c (com.mabopractice.dialoglayout:id/frameContainer) for fragment FirstFragment{ee2567d (35a1ef4a-ff9a-4eac-8584-16d04162b1b0) id=0x7f07005c}

수정 전
```kotlin
childFragmentManager.beginTransaction().apply {
    replace(R.id.frameContainer, FirstFragment())
    commit()
}
```


수정 후
```kotlin
activity?.supportFragmentManager?.beginTransaction()?.apply {
    replace(R.id.frameContainer, FirstFragment())
    commit()
}
```

참고 자료
https://cmsdk.com/java/android-fragment-no-view-found-for-id.html