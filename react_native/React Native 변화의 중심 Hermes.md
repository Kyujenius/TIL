## React Native 변화의 중심 Hermes

개발자라면 누구나 **성능**에 대한 고민을 한다. 특히 크로스 플랫폼 개발에서는 네이티브 앱과 비교했을 때 성능 차이가 항상 아킬레스건이었다. React Native는 JavaScript를 사용해 iOS와 Android 앱을 동시에 개발할 수 있는 혁신적인 프레임워크지만, 그동안 *성능 문제*로 많은 개발자들의 한숨을 자아냈다.

하지만 2019년, Meta(당시 Facebook)가 **Hermes**라는 새로운 JavaScript 엔진을 공개하였고 **(오픈소스화는 2022년에 했습니다.)** 처음엔 욕을 엄청 먹었지만, 안정화가 되면서 점차 React Native의 판도가 완전히 바뀌었다. 이제 React Native 앱은 Swift나 Kotlin으로 개발된 네이티브 앱과 견줄만한 성능을 보여준다. 어떻게 이런 변화가 가능했을까?!

## 💡급하신 분들을 위해서 결론 먼저!

1. Hermes는 React Native 앱의 시작 시간을 최대 70% 단축할만큼 큰 변화를 주도하고 있으며 오픈소스로 이루어져 있다.
2. 성능 개선의 주된 이유는 JIT(Just In Time) 에서 AOT(Ahead-Of-Time) 컴파일 방식으로 바꾸며, 실행 속도를 혁신적으로 개선하기 때문이다.
3. Hermes는 Hades 라는 가비지 컬렉터를 사용하는데, 이는 추후에 블로그로 자세히 한 번 더!
4. 앱 크기와 메모리 사용량을 대폭 줄여 저사양 기기에서도 원활한 실행을 보장한다.
5. React Native 0.70부터 기본 JavaScript 엔진으로 채택되었다.
6. Swift나 Kotlin으로 개발된 네이티브 앱과 견줄만한 성능을 제공한다.

## 1. Hermes란 무엇인가?

### Hermes의 탄생 배경 - JSC의 약점 보완

React Native 앱은 원래 JavaScriptCore(JSC)라는 JavaScript 엔진을 사용했다. 이 엔진은 iOS에 기본 탑재된 엔진으로, Android에서도 동일한 경험을 제공하기 위해 React Native에 포함되었다. 하지만 JSC는 모바일 환경에 최적화되지 않았고, 특히 **런타임에 JavaScript를 컴파일하는 방식인 JIT 즉, Just In Time 컴파일** 하는 방식으로 인해 앱 시작 시간이 느리다는 큰 단점이 있었다.

> ![](https://velog.velcdn.com/images/kyujenius/post/f7ece48b-9a7f-4e4c-bcff-d1e6d51e269b/image.png)
> JSC의 특징 도식화

Meta는 `Facebook Marketplace` 앱을 `React Native`로 개발하면서 이러한 문제점을 직접 경험했고, 결국 모바일에 최적화된 `새로운 JavaScript 엔진`의 필요성을 느꼈다. (문제를 체감하자마자, 틀을 뜯어고친다는 게 정말 세계적인 기업인 이유가 있지 않나 싶다.)

### Hermes의 핵심 특징

Hermes는 **모바일 환경에 최적화된 오픈소스 JavaScript 엔진**이다. 가장 큰 특징은 다음과 같다:

1. **AOT(Ahead-Of-Time) 컴파일**: 앱 빌드 시점에 JavaScript를 바이트코드로 미리 컴파일한다.
2. **빠른 시작 시간**: 런타임에 컴파일할 필요가 없어 앱 시작 속도가 크게 향상된다.
3. **낮은 메모리 사용량**: 메모리 사용을 최소화하도록 설계되어 저사양 기기에서도 원활하게 작동한다.
4. **작은 바이너리 크기**: APK 크기를 줄여 다운로드 시간과 저장 공간을 절약한다.

> (👨🏻‍🏫 : 간단히 말하자면, 앱이 실행되기 전에 어려운 숙제를 미리 다 해놓는 똑똑한 엔진이라고 파악하면 좋지 않을까요?)
> ![](https://velog.velcdn.com/images/kyujenius/post/54272f5d-563c-410c-aa63-630cee0f346f/image.png)

## 2. Hermes의 성능 개선 원리

## 놀라운 성능 개선 사례

Hermes의 도입으로 인한 성능 개선은 실로 놀랍다. Reddit의 한 개발자는 JSC에서 Hermes로 전환한 후 앱 시작 시간이 **12.9초에서 3.9초로 70% 단축**되었다고 보고했다. 이는 특히 저사양 Android 기기에서 더욱 두드러진 효과를 보인다.

[Our app is 70% faster to load with Hermes!](https://www.reddit.com/r/reactnative/comments/n5e7jo/our_app_is_70_faster_to_load_with_hermes/)

Meta의 내부 측정에 따르면 Hermes는 다음과 같은 개선을 가져왔다:

| 측정 항목         | JSC     | Hermes  | 개선율             |
| ----------------- | ------- | ------- | ------------------ |
| 앱 시작 시간(TTI) | 더 느림 | 더 빠름 | 최대 70% 향상      |
| APK 크기          | 더 큼   | 더 작음 | 크기 감소          |
| 메모리 사용량     | 더 많음 | 더 적음 | 메모리 효율성 증가 |

Hermes가 이러한 성능 향상을 가져올 수 있는 비결은 **AOT 컴파일 방식**에 있다. 기존 JSC는 앱이 실행될 때 JavaScript 코드를 해석하고 컴파일하는(JIT) 과정을 거쳤지만, Hermes는 앱 빌드 시점에 JavaScript를 효율적인 바이트코드로 미리 변환한다.

## Hermes의 작동 원리

**기존의 JSC 엔진의 파이프라인**

![](https://velog.velcdn.com/images/kyujenius/post/2855509a-b903-44b8-97cf-e53f591276ef/image.png)

**현재 Hermes 엔진의 파이프라인**

![](https://velog.velcdn.com/images/kyujenius/post/d1fa81ec-c225-40da-885b-dadf891ae73a/image.png)

이 방식은 다음과 같은 이점을 제공한다:

- **빠른 앱 시작**: 런타임에 컴파일할 필요가 없어 앱이 더 빨리 시작된다
- **효율적인 메모리 사용**: 최적화된 바이트코드는 메모리를 적게 사용한다
- **작은 앱 크기**: 컴파일된 바이트코드는 원본 JavaScript보다 크기가 작다

> (👨🏻‍🏫 : 런타임 과정에 실행하는 작업이 많이 줄어든 것을 볼 수 있죠? 마치 수학 시험을 볼 때, 공식을 외우지 않아, 그 자리에서 공식을 증명하는 것과, 시험 전에 공식을 미리 외워가는 차이와도 같답니다. 외워두기만 하면 시험장에서 공식을 유도할 필요 없이 바로 문제를 풀 수 있죠!)

## 3. Hermes의 혁신적인 가비지 컬렉터, Hades

### Hades의 등장 배경

Hermes는 처음에 **GenGC**(Generational Garbage Collector)라는 가비지 컬렉터를 사용했다. 하지만 GenGC는 단일 스레드에서 작동하며 메인 JavaScript 스레드에서 실행되어 긴 Garbage Collector 내에서 일시 정지(pause) 현상을 일으켰다 Facebook의 관찰에 따르면 이 일시 정지는 평균 200ms, 최악의 경우 1.4초, 때로는 7초까지 지속되었다
[Facebook의 관찰, Garbage Collector 내에서 일시 정지(pause) 현상 관측](https://www.mwanmobile.com/what-makes-hermes-engine-react-native-fast/).

이러한 GenGC의 한계를 극복하기 위해 Meta는 2021년 React Native 0.65 버전에서 **Hades**라는 새로운 동시성 가비지 컬렉터를 도입했다

[하데스 공식 문서](https://github.com/facebook/hermes/blob/main/doc/Hades.md)

### Hades의 혁신적 특징 및 Hades의 작동 원리

> (👨🏻‍🏫 : 는 추후에 블로그로 더 자세히 다뤄보겠습니다..!!)

## 4. Hermes 적용하기(최신 버전으로 시작하면, 자동으로 적용됨)

[Using Hermes in React Native - LogRocket Blog](https://blog.logrocket.com/using-hermes-react-native/)

### Android에서 Hermes 활성화하기

React Native 0.70 버전부터 Hermes는 기본 JavaScript 엔진으로 설정되어 있다. 하지만 이전 버전을 사용하거나 명시적으로 설정하고 싶다면 다음과 같이 할 수 있다:

```jsx
// android/app/build.gradle 파일에 추가
project.ext.react = [
    entryFile: "index.js",
    enableHermes: true  // Hermes 활성화
]
// 출처: LogRocket Blog
```

### iOS에서 Hermes 활성화하기

iOS에서는 React Native 0.64 버전부터 Hermes를 사용할 수 있게 되었다. Podfile에 다음과 같이 설정한다:

```jsx
*# ios/Podfile 파일에 추가*
use_react_native!(
  :path => config[:reactNativePath],
  :hermes_enabled => true  *# Hermes 활성화*
)
// 출처: LogRocket Blog
```

### Expo 프로젝트에서 Hermes 사용하기

Expo에서는 Hermes가 기본 JavaScript 엔진으로 설정되어 있다. 특정 플랫폼에서만 Hermes를 사용하거나 비활성화하려면 app.json 파일에서 설정할 수 있다:

```jsx
{
  "expo": {
    "jsEngine": "hermes",
    "ios": {
      "jsEngine": "jsc"  *// iOS에서만 JSC 사용*
    }
  }
}
*// 출처: Expo 공식 문서*
```

## 5. Hermes 사용 시 주의사항

### 호환성 이슈

Hermes는 대부분의 JavaScript 기능을 지원하지만, 일부 라이브러리나 기능은 호환성 문제가 있을 수 있다. 특히:

- **일부 npm 패키지**: react-hook-forms, Realm 등은 Hermes와 함께 사용할 때 추가 설정이 필요할 수 있다
- **디버깅 경험**: Flipper와 함께 사용할 때 간헐적인 충돌이 발생할 수 있다.
- **바이트코드 버전 호환성**: Hermes 버전이 다르면 바이트코드 형식이 호환되지 않을 수 있다.

### 업데이트 관리

Expo를 사용하는 경우, Hermes 바이트코드 형식은 버전마다 달라질 수 있으므로 `runtimeVersion`을 적절히 관리해야 한다. React Native 버전을 업데이트할 때는 Hermes 버전도 함께 업데이트되므로 주의해야 한다.

## 5. Hermes의 미래

### 지속적인 발전

Meta는 Hermes를 지속적으로 개선하고 있다. 최근에는 다음과 같은 발전이 있었다:

- **iOS 지원 안정화**: React Native 0.64에서 처음 도입된 iOS 지원이 점차 안정화되고 있다.
- **성능 최적화**: 정적 타입 분석을 활용한 JavaScript 실행 최적화.
- **기본 엔진으로 채택**: React Native 0.70부터 기본 JavaScript 엔진으로 채택됨

### React Native의 경쟁력 강화

Hermes의 도입으로 React Native는 Swift나 Kotlin으로 개발된 네이티브 앱과 견줄만한 성능을 제공하게 되었다. 이는 크로스 플랫폼 개발의 가장 큰 단점이었던 성능 문제를 크게 개선한 것으로, React Native의 경쟁력을 한층 강화했다.

Hermes의 등장은 React Native 생태계에 새로운 활력을 불어넣었다. 이제 개발자들은 JavaScript의 생산성과 네이티브 앱의 성능을 동시에 누릴 수 있게 되었다. 앞으로 Hermes가 React Native 생태계에 어떤 혁신을 더 가져올지 기대된다. 아무래도 오픈소스니까, 많은 이들의 참여를 통해 보다 더 빠르게 성장할 것이다.

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
