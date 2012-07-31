# Core Push Android SDK

##概要

Core Push Android SDK は、プッシュ通知ASPサービス「CORE PUSH」の Android用のSDKになります。ドキュメントは CORE PUSH Developer サイトに掲載しております。

 
■公式サイト

CORE PUSH：<a href="http://core-asp.com">http://core-asp.com</a>

CORE PUSH Developer（開発者向け）：<a href="http://developer.core-asp.com">http://developer.core-asp.com</a>##動作条件
* GCM方式による通知はAndroid2.2以上が動作対象になります。* SDK/Eternal/gcm.jar をプロジェクトのlibsフォルダにコピーしてください。* SDK/corepush.jar を プロジェクトの libsフォルダにコピーしてください。##アプリの通知設定

Core Push Android SDKを利用するための設定を行います。

###ApplicationContext.xmlの設定

* GCM方式による通知は Android 2.2以上で動作するため、minSdkVersion に8以上の値を指定してください。

```
	<!-- SDKの最小のAPIレベルを指定。C2DMはAPIレベル8以上で動作可能 -->
	<uses-sdk android:minSdkVersion="8" android:targetSdkVersion="16"/>
```

* 実行するアプリケーションのみが通知を受信するために、以下のパーミッションを指定してください。android:name のcom.coreasp.corepushsample の部分は実行するアプリケーションのパッケージ名に置き換えてください。

```	 <permission android:name="com.coreasp.corepushsample.permission.C2D_MESSAGE" android:protectionLevel="signature" />    <uses-permission android:name="com.coreasp.corepushsample.permission.C2D_MESSAGE" />
```

* その他、通知の利用の際に必要な以下のパーミッションを指定してください。

```
    <!-- インターネット接続のパーミッション設定 -->    <uses-permission android:name="android.permission.INTERNET"></uses-permission>    <!-- GCM requires a Google account. -->    <uses-permission android:name="android.permission.GET_ACCOUNTS" />    <!-- Keeps the processor from sleeping when a message is received. -->    <uses-permission android:name="android.permission.WAKE_LOCK" />
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
  	<!-- GCM用のインテントサービスを設定 -->    <service android:name="com.coreasp.CorePushIntentService" />```

以下に、サンプルプロジェクトのCorePushSample で利用している ApplicationManifest.xml の内容を記載します。

```	<?xml version="1.0" encoding="utf-8"?>	<manifest xmlns:android="http://schemas.android.com/apk/res/android"    package="com.coreasp.corepushsample"    android:versionCode="1"    android:versionName="1.0" >    <!-- SDKの最小のAPIレベルを指定。C2DMはAPIレベル8以上で動作可能 -->    <uses-sdk android:minSdkVersion="8" android:targetSdkVersion="16"/>        <!-- インターネット接続のパーミッション設定 -->    <uses-permission android:name="android.permission.INTERNET"></uses-permission>    <!-- GCM requires a Google account. -->    <uses-permission android:name="android.permission.GET_ACCOUNTS" />    <!-- Keeps the processor from sleeping when a message is received. -->    <uses-permission android:name="android.permission.WAKE_LOCK" />        <!-- 通知時のバイブレーションのパーミッション設定  -->	<uses-permission android:name="android.permission.VIBRATE" />        	<!-- C2DMのパーミッション設定 -->    <permission android:name="com.coreasp.corepushsample.permission.C2D_MESSAGE" android:protectionLevel="signature" />    <uses-permission android:name="com.coreasp.corepushsample.permission.C2D_MESSAGE" />    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />        <application        android:icon="@drawable/ic_launcher"        android:label="@string/app_name"  android:debuggable="true">                <!-- 初回起動のアクティビティを設定 -->        <activity            android:label="@string/app_name"            android:name=".NotificationActivity" >            <intent-filter >                <action android:name="android.intent.action.MAIN" />                <category android:name="android.intent.category.LAUNCHER" />            </intent-filter>        </activity>                <!-- GCM用のブロードキャストレシーバーを設定 -->        <receiver            android:name="com.coreasp.CorePushBroadcastReceiver"            android:permission="com.google.android.c2dm.permission.SEND" >            <intent-filter>                <!-- Receives the actual messages. -->                <action android:name="com.google.android.c2dm.intent.RECEIVE" />                <!-- Receives the registration id. -->                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />                <category android:name="com.coreasp.corepushsample" />            </intent-filter>        </receiver>
	    	<!-- GCM用のインテントサービスを設定 -->        <service android:name="com.coreasp.CorePushIntentService" />            </application></manifest>```###CORE PUSHの設定キーの指定
Core Push管理画面 にログインし、ホーム画面からAndroidアプリの設定キーを確認してください。 この設定キーをCorePushManager#setConfigKey で指定します。	
	CorePushManager.getInsance().setConfigKey("XXXXXXXXXX");###SenderId(ProjectId)の指定
<a href="http://developer.core-asp.com/gcm.php">Project ID（アプリ）とAPI Key（管理画面）の作成方法</a> を参考に プロジェクトIDを取得し、CorePushManager#setSenderIdで指定します。	CorePushManager.getInstance().setSenderId("1234567890");	###通知から起動するアクティビティの指定
ステータスバーの通知をタップした後に起動するアクティビティのクラスを CorePushManager#setActivity で指定します。
	CorePushManager.getInstance().setActivity(NotificationActivity.class);
###通知アイコンの指定
通知時のステータスバーに表示されるアイコンのリソースIDを指定します。
	CorePushManager.getInstance().setIconResourceId(R.drawable.ic_launcher);
##デバイスの通知登録解除
デバイスが通知を受信できるようにするには、CORE PUSH にデバイストークンを送信します。またデバイスが通知を受信できないようにするには、CORE PUSH からデバイストークンを削除します。

###デバイストークンの登録
CORE PUSH にデバイストークンを登録するには、CorePushManager#registToken を呼び出します。
	
	CorePushManager.getInstance().registToken(this);
	
本メソッドはアプリの初回起動時かON/OFFスイッチなどで通知をONにする場合に使用してください。		###デバイストークンの削除
CORE PUSH からデバイストークンを削除するには、CorePushManager#removeToken を呼び出します。
	CorePushManager.getInstance().removeToken(this);		
本メソッドはON/OFFスイッチなどで通知をOFFにする場合に使用してください。
				