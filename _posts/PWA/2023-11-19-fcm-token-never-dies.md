---
title: "fcm 토큰 언제까지 살아 있어? 문의해 보았다."
excerpt: "iOS safari에서 fcm 토큰 문제"

categories:
  - PWA
tags:
  - PWA-Push-iOS
  - safari
toc: true
sidebar_main: true
---

예상치 못했고 무서운 일이 일어났다.

앱을 삭제하고 캐시와 데이터를 지워도 fcm 토큰이 살아있다.. 좀비 토큰 ㅠㅠ

ios 에서 발생!!!

(안드로이드는 삼성 브러우저가 좀비 토큰 현상 있었지만, [여러가지 이유](/pwa/samsung-browser-as-pwa)로 삼성브라우저는 차단하기로 했기 때문에 넘어감)

## 테스트하기

삭제한 앱의 토큰에 푸시를 보냈다. 잘 갔다는 성공!이 돌아온다.

테스트를 매일 했는데 약 7~30일 후에는.. 비활성화 되는거 같긴 하다. (정확하지는 않음) 비활성화되면 그때 푸시는 비로소 오류를 돌려준다.

다른 pwa를 이용한 푸시 서비스를 이용해서 테스트 앱을 만들고 대시보드 확인을 해봤다. 확실히 iOS는 앱 삭제 여부가 정확하지 않았다.

## 문의하기

이런 날이 오게 될 줄이야. 지금까지 겪었던 문제는 약간만 검색하면 나오는 것들이어서 문의할 일이 없었는데.

### pwa push 보내주는 모 서비스

업체 이름을 밝히지는 않겠지만, iOS의 푸시 도달과 클릭 정보가 정확하지 않다고 문의를 보내봤다. 앱을 삭제 했는데 푸시 성공카운트 된다고..

확인해 보고 오겠다고 답변 받았는데 그 뒤로 몇 달동안 무소식이다. 서비스의 코드를 확인해 보니, 특별한 점은 없었고 push 이벤트와 notificationclick 이벤트에서 확인 요청을 보내고 있기는 했다. 단지 이게 iOS에서는 착실히 작동을 안한다는 점.

### 파이어베이스

이 문제가 지속되고 해결 방법을 못 찾겠어서, 파이어베이스와 애플 개발자 지원에 문의를 했다. 파이어베이스에 먼저 문의했다. 답변에는 여러 포럼 자료를 링크해 주셨고, 토큰이 오래된 것인지 판단하는 임계값을 설정해야 한다고 해주셨다. 2개월이라는 구체적인 숫자를 권장했다. pwa 삭제를 감지를 구현해야 하지만 방법은 확실치 않고 애플에 문의하는 게 가장 좋을 것 같다는 내용이었다.

### 애플 개발자 지원

그래서 다음날 애플에 문의를 했는데, 애플에서 확인하거나 지원해 줄 수 없다는 내용 뿐이다. 이런.. 영어로 물어볼걸 그랬나 그러면 더 친절한가(?)

### 애플 개발자 포럼

여전히 아무도 답이 없고.. 대체로 앱 푸시 관련 내용이랑 섞여서 질문을 걸러서 찾아보는 것도 어려웠다.

## 추가 검색 결과

일단 사파리는 사용자 상호 작용 없이 사파리를 사용한 지 7일이 지나면, 자바스크립트로 쓰기 가능한 저장소의 데이터가 삭제된다고 한다. 하지만 pwa는 사파리의 일부가 아니고 저장소가 분리되어 있으므로 [해당 정책](https://webkit.org/tracking-prevention/#intelligent-tracking-prevention-itp)에 영향을 받지 않는다. 데이터가 삭제되지는 않을 것..이라고 한다.

앱을 삭제했을 때는 데이터도 좀 지워주지.. 아니면 사파리처럼 직접 설정에서 데이터 삭제를 할 수 있게 하면 좋을 텐데. 웹 푸시가 되는 것으로 이제 pwa 지원을 해줄 거 같아서 앞으로 개선되었으면 좋겠다.

## 결론

사파리가 업데이트 해주기 전까지는, iOS에서 pwa 삭제는 감지할 수 없는 것으로.

관련된 문제: 문자 알림을 웹푸시로 대체할 수 없다.
