# Challenge_09_NotificationReceiver
## 푸시알림 수신기

Firebase Cloud Messaging을 이용한 푸시알림 테스트용 어플이다.

이 프로젝트에서는 푸시알림의 발송과 수신방식을 이해하는데에 중점을 두었다.


## 기능

기기의 Firebase 토큰을 입력 후 해당 기기에 FCM을 통해 푸시 알림을 보내는 기능을 제공한다.

푸시 알림의 종류는 일반(NORMAL), 확장형(EXPANDABLE), 커스텀(CUSTOM)을 선택하여 전송이 가능하다.

해당 푸시 알림을 누르면 어떤 종류의 푸시알림을 통해 앱을 실행하였는지 확인할 수 있고, 앱을 처음 실행한것인지(앱 종료 후 실행한것인지) 혹은 앱이 실행중인 상태에서 갱신이 된 것인지 확인 할 수 있다.

## 학습 내용

### Firebase Cloud Messaging
* 기기의 Firebase Token을 가져오자
  ``` kotlin
  FirebaseMessaging.getInstance().token
            .addOnCompleteListener { task ->
                if(task.isSuccessful){
                    firebaseToken.text = task.result
                }
            }
  ```
* NotificationType enum class를 생성한다.
  ``` kotlin
  enum class NotificationType(val title: String, val id: Int) {
    NORMAL("일반 알림", 0),
    EXPANDABLE("확장형 알림", 1),
    CUSTOM("커스텀 알림", 3)
  }
  ```
* CHANNEL_NAME, CHANNEL_DESCRIPTION, CHANNEL_ID를 상수로 정의한다.
  ``` kotlin
  companion object{
        private const val CHANNEL_NAME = "Emoji Party"
        private const val CHANNEL_DESCRIPTION = "Emoji Party를 위한 채널"
        private const val CHANNEL_ID = "Channel Id"
  }
  ```

* NotificationChannel을 생성한다.
  * Channel은 Oreo버전 이상에서만 가능하다.
  ``` kotlin
  private fun createNotificationChannel(){
        if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(
                CHANNEL_ID,
                CHANNEL_NAME,
                NotificationManager.IMPORTANCE_DEFAULT
            )
            channel.description = CHANNEL_DESCRIPTION

            (getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager)
                .createNotificationChannel(channel)
        }
    }
  ```
* Notification을 생성한다.
  ``` kotlin
  private fun createNotification(type: NotificationType,
                                   title: String?,
                                   message: String?
    ): Notification {
        val intent = Intent(this, MainActivity::class.java).apply{
            putExtra("notificationType","${type.title}타입")
            addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP)
        }
        val pendingIntent = PendingIntent.getActivity(this,type.id,intent,FLAG_UPDATE_CURRENT)

        val notificationBuilder = NotificationCompat.Builder(this, CHANNEL_ID)
            .setSmallIcon(R.drawable.ic_baseline_notifications_24)
            .setContentTitle(title)
            .setContentText(message)
            .setPriority(NotificationCompat.PRIORITY_DEFAULT)
            .setContentIntent(pendingIntent)
            .setAutoCancel(true)

        when(type) {
            NotificationType.NORMAL -> Unit
            NotificationType.EXPANDABLE -> {
                notificationBuilder.setStyle(
                    NotificationCompat.BigTextStyle()
                        .bigText(
                            "확장형 알림 내용 예시입니다😁👍😊👏😂🤣❤" +
                            "긴 내용은 가독성을 위해😍😒👌👌😘💕👍😊👏👏😂🤣❤" +
                            "나누었습니다.😍😒😘😁😁😁😁😁❤️❤️❤️❤"
                        )
                )
            }
            NotificationType.CUSTOM -> {
                notificationBuilder
                    .setStyle(NotificationCompat.DecoratedCustomViewStyle())
                    .setCustomContentView(
                        RemoteViews(
                            packageName,
                            R.layout.view_custom_notification
                        ).apply{
                            setTextViewText(R.id.title, title)
                            setTextViewText(R.id.message, message)
                        }
                    )

            }
        }
        return notificationBuilder.build()
    }
  ```
* FCM을 통해 메시지를 받았을경우 어떻게 처리할지 정의한다.
  ``` kotlin
  override fun onMessageReceived(remoteMessage: RemoteMessage) {
        super.onMessageReceived(remoteMessage)

        createNotificationChannel()

        val type = remoteMessage.data["type"]
            ?.let{ NotificationType.valueOf(it) }
        val title = remoteMessage.data["title"]
        val message = remoteMessage.data["message"]

        type ?: return


        NotificationManagerCompat.from(this)
            .notify(type.id, createNotification(type, title, message))
    }
  ```
* Firebase에서 제공하는 API를 통해 JSON형태로 전송한다.(이때 Token은 기기의 메시지를 받을 기기의 Token을 넣어준다.)
  ``` json
  {
   "message": {
    "token": "기기의 토큰",
    "data": {
      "title": "Test Title",
      "message": "Test Message"
      }
    }
  }
  ```
### Layout
<img src="https://user-images.githubusercontent.com/74666576/154559519-439b579b-77d1-4647-bce6-b2969ed1ff0b.jpg">

* ### MarginStart? MarginLeft? 차이가 뭐지? 같은거 아닌가?
  UI를 구상하다 보면 MarginStart와 MarginLeft를 사용해 본적이 있을 것이다. 어쩌면 많은 사람들이 둘의 차이를 느끼지 못할 수도 있다. 왜 그런것인지 알아보면 언어를 읽는 방식에 달려있다. 
  
  한국어나 영어 등 대부분의 언어는 왼쪽에서 오른쪽으로 읽는다. 이것을 LTR방식이라 한다. 반면에 아랍어 같은 경우는 오른쪽에서 왼쪽으로 읽는 RTL방식을 사용한다. 안드로이드 스튜디오는 이점을 감안하여 레이아웃 미러링을 지원한다.(레이아웃 미러링은 API수준 17이상에서 지원된다.) 우리가 MarginStart와 MarginLeft을 사용하는 것이 레이아웃 미러링이다. 
  
  MarginStart는 사용자 기기의 설정되어 있는 값으로 UI컴포넌트가 적용된다. 예로 MarginStart의 경우 사용자의 기기의 언어설정이 한국어로 되어있다면 Margin값을 왼쪽을 기준으로 잡는다. 반면에 아랍어로 설정 되어있다면 오른쪽을 기준으로 잡는 것이다. 
  
  PaddingStart/PaddingLeft, PaddingEnd/PaddingRight 도 이와 같은 개념이다. 자세한 설명은 공식문서에서 확인이 가능하다.
  * 이미지 출처 및 공식 문서 https://developer.android.com/training/basics/supporting-devices/languages 
 
![화면 캡처 2022-02-18 045519](https://user-images.githubusercontent.com/74666576/154560671-5ce6086f-fb3b-406f-850e-9fb6ffad49bc.jpg) 
![화면 캡처 2022-02-18 045546](https://user-images.githubusercontent.com/74666576/154560665-819e3408-affe-4625-b921-71e15a1227e4.jpg)


▼ 표 1. 앱이 여러 텍스트 방향을 지원하는 경우 사용하는 속성

|LTR만 지원하는 속성|LTR 및 RTL을 지원하는 속성|
|:---:|:---:|
|android:gravity="left"|android:gravity="start"|
|android:gravity="right"|android:gravity="end"|
|android:layout_gravity="left"|android:layout_gravity="start"|
|android:layout_gravity="right"|android:layout_gravity="end"|
|android:paddingLeft|android:paddingStart|
|android:paddingRight|android:paddingEnd|
|android:drawableLeft|android:drawableStart|
|android:drawableRight|android:drawableEnd|
|android:layout_alignLeft|android:layout_alignStart|
|android:layout_alignRight|android:layout_alignEnd|
|android:layout_marginLeft|android:layout_marginStart|
|android:layout_marginRight|android:layout_marginEnd|
|android:layout_alignParentLeft|android:layout_alignParentStart|
|android:layout_alignParentRight|android:layout_alignParentEnd|
|android:layout_toLeftOf|android:layout_toStartOf|
|android:layout_toRightOf|android:layout_toEndOf|
                            
## 실행화면

