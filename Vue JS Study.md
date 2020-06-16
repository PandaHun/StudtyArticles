# Vue Js

---

Vue.js를 공부하며 남기는 Articles

## Vue Overview

* UI(User Interface)를 위한 프레임워크
  * SPA(Sing Page Application)을 완벽하게 지원할 수 있다
* Component로 적용된 시스템
  * Vue에서의 Component는 미리 정의된 옵션을 가진 Vue 인스턴스.

## Vue Life Cycles

 vue의 라이프 사이클은 크게 

* Create
* Mount
* Update
* Destruct

4가지로 나눌 수 있다.

![Vue life cycle](https://kr.vuejs.org/images/lifecycle.png)

### 1. Create

라이프 사이클에서 가장 처음 실행된다. 이 단계는 컴포넌트가 DOM에 추가되기 전으로 `$el`을 사용할 수 없다.

이 단계에는 `beforeCreated`,`Created`가 있다.

#### beforeCreated

모든 라이프 사이클 훅 중에서 가장 먼저 실행되고, 다른 `data`,`events`에 접근하고자 하면 에러가 난다.

#### Created

이제는 `data`,`events`가 활성화되어 접근할 수 있으나, 템플릿과 DOM은 마운트 및 렌더링 되지 않았다.

### 2. Mount( DOM 삽입 단계 )

