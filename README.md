# Core Push Android SDK

##概要

Core Push Android SDK は、プッシュ通知ASPサービス「CORE PUSH」の Android用のSDKになります。


Core Push Android SDKを利用するための設定を行います。

###ApplicationContext.xmlの設定

* GCM方式による通知は Android 2.2以上で動作するため、minSdkVersion に8以上の値を指定してください。

```
	<!-- SDKの最小のAPIレベルを指定。C2DMはAPIレベル8以上で動作可能 -->
	<uses-sdk android:minSdkVersion="8" android:targetSdkVersion="16"/>
```

* 実行するアプリケーションのみが通知を受信するために、以下のパーミッションを指定してください。android:name のcom.coreasp.corepushsample の部分は実行するアプリケーションのパッケージ名に置き換えてください。

```
```

* その他、通知の利用の際に必要な以下のパーミッションを指定してください。

```
    <!-- インターネット接続のパーミッション設定 -->
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />    
```


* ブロードキャストレシーバを指定してください。 category のandroid:name の com.coreasp.corepushsmapleの部分は実行するアプリケーションのパッケージ名に置き換えてください。また、receive の android:name には SDK内の com.coreasp.CorePushBroadcastReceiver を指定してください。

```
    <!-- GCM用のブロードキャストレシーバーを設定 -->
    <receive
        android:name="com.coreasp.CorePushBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND" >
        <intent-filter>
            <!-- Receives the actual messages. -->
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <!-- Receives the registration id. -->
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="com.coreasp.corepushsample" />
        </intent-filter>
	</receiver>

```
* インテントサービスを指定してください。service の andorid:name には SDK内の com.coreasp.CorePushIntentService を指定してください。

```
  	<!-- GCM用のインテントサービスを設定 -->

以下に、サンプルプロジェクトのCorePushSample で利用している ApplicationManifest.xml の内容を記載します。

```
	


<a href="http://developer.core-asp.com/gcm.php">Project ID（アプリ）とAPI Key（管理画面）の作成方法</a> を参考に プロジェクトIDを取得し、CorePushManager#setSenderIdで指定します。






デバイスが通知を受信できるようにするには、CORE PUSH にデバイストークンを送信します。またデバイスが通知を受信できないようにするには、CORE PUSH からデバイストークンを削除します。

###デバイストークンの登録
CORE PUSH にデバイストークンを登録するには、CorePushManager#registToken を呼び出します。
	
	CorePushManager.getInstance().registToken(this);
	
本メソッドはアプリの初回起動時かON/OFFスイッチなどで通知をONにする場合に使用してください。
CORE PUSH からデバイストークンを削除するには、CorePushManager#removeToken を呼び出します。

本メソッドはON/OFFスイッチなどで通知をOFFにする場合に使用してください。
