# Challenge_09_NotificationReceiver
## 푸시알림 수신기

Firebase Cloud Messaging을 이용한 푸시알림 테스트용 어플이다.

이 프로젝트에서는 푸시알림의 발송과 수신방식을 이해하는데에 중점을 두었다.


## 기능

기기의 Firebase 토큰을 입력 후 해당 기기에 FCM을 통해 푸시 알림을 보내는 기능을 제공한다.

푸시 알림의 종류는 일반(NORMAL), 확장형(EXPANDABLE), 커스텀(CUSTOM)을 선택하여 전송이 가능하다.

해당 푸시 알림을 누르면 어떤 종류의 푸시알림을 통해 앱을 실행하였는지 확인할 수 있고, 앱을 처음 실행한것인지(앱 종료 후 실행한것인지) 혹은 앱이 실행중인 상태에서 갱신이 된 것인지 확인 할 수 있다.

## 학습 내용

* ### MarginStart? MarginLeft? 차이가 뭐지? 같은거 아닌가?
  * UI를 구상하다 보면 MarginStart와 MarginLeft를 사용해 본적이 있을 것이다. 많은 개발자들이 둘의 차이를 느끼지 못하는것이 일반적일 것이다. 왜 그런것인지 알아보면 한국어나 영어 등 대부분의 언어는 왼쪽에서 오른쪽으로 읽는다. 이것을 LTR방식이라 한다. 반면에 아랍어 같은 경우는 오른쪽에서 왼쪽으로 읽는 RTL방식을 사용한다. 안드로이드 스튜디오는 이점을 감안하여 MarginStart와 MarginLeft를 구분하였다. MarginStart는 사용자 기기의 설정되어 있는 값으로 UI컴포넌트가 적용된다. 예로 MarginStart의 경우 사용자의 기기의 언어설정이 한국어로 되어있다면 Margin값을 왼쪽을 기준으로 잡는다. 반면에 아랍어로 설정 되어있다면 오른쪽을 기준으로 잡는 것이다. PaddingStart/PaddingLeft, PaddingEnd/PaddingRight 도 이와 같은 개념이다. 공식문서에서도 확인이 가능하다.
  * 
  * https://developer.android.com/training/basics/supporting-devices/languages 
 
                           
                            
## 실행화면
