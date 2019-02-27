# Etape 1 
[Ajoutez Firebase dans votre projet](https://firebase.google.com/docs/android/setup).

# Etape 2
Ajoutez dans build.gradle niveau app
```sh
    implementation 'com.google.firebase:firebase-core:16.0.6'
    implementation 'com.google.firebase:firebase-iid:17.0.4'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    implementation 'com.firebase:firebase-jobdispatcher:0.8.5'

```

# Etape 3 
Ajoutez dans AndroidManifest.xml
```sh
  <uses-permission android:name="android.permission.INTERNET"/>
  <application>
  ....
    <!-- [START fcm_default_icon] -->
        <!-- Set custom default icon. This is used when no icon is set for incoming notification messages.
             See README(https://goo.gl/l4GJaQ) for more. -->
        <meta-data
            android:name="com.google.firebase.messaging.default_notification_icon"
            android:resource="@drawable/ic_stat_ic_notification" />
        <!-- Set color used with incoming notification messages. This is used when no color is set for the incoming
             notification message. See README(https://goo.gl/6BKBk7) for more. -->
        <meta-data
            android:name="com.google.firebase.messaging.default_notification_color"
            android:resource="@color/colorAccent" />
        <!-- [END fcm_default_icon] -->
        <!-- [START fcm_default_channel] -->
        <meta-data
            android:name="com.google.firebase.messaging.default_notification_channel_id"
            android:value="@string/default_notification_channel_id" />
        <!-- [END fcm_default_channel] -->
        
          <!-- [START firebase_service] -->
        <service android:name=".java.MyFirebaseMessagingService">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
        <!-- [END firebase_service] -->
   ....
 </application>
```

# Etape 4
Creer la classe MyFirebaseMessagingService  qui extend de FirebaseMessagingService et ajoutez dans cette classe la fonction et appelez la dans onmessagereceived pour faire la generation de votre propre notification

```sh
     /**
     * Create and show a simple notification containing the received FCM message.
     *
     * @param messageBody FCM message body received.
     */
    private void sendNotification(String messageBody) {
        Intent intent = new Intent(this, MainActivity.class);
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        PendingIntent pendingIntent = PendingIntent.getActivity(this, 0 /* Request code */, intent,
                PendingIntent.FLAG_ONE_SHOT);

        String channelId = getString(R.string.default_notification_channel_id);
        Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
        NotificationCompat.Builder notificationBuilder =
                new NotificationCompat.Builder(this, channelId)
                        .setSmallIcon(R.drawable.ic_stat_ic_notification)
                        .setContentTitle(getString(R.string.fcm_message))
                        .setContentText(messageBody)
                        .setAutoCancel(true)
                        .setSound(defaultSoundUri)
                        .setContentIntent(pendingIntent);

        NotificationManager notificationManager =
                (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);

        // Since android Oreo notification channel is needed.
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            NotificationChannel channel = new NotificationChannel(channelId,
                    "Channel human readable title",
                    NotificationManager.IMPORTANCE_DEFAULT);
            notificationManager.createNotificationChannel(channel);
        }

        notificationManager.notify(0 /* ID of notification */, notificationBuilder.build());
    }
}
```
# Etape 5
Si vous souhaitez envoyer des messages à cette instance d'application ou gérez les abonnements à cette application côté serveur, envoyez le jeton d'identifiant d'instance sur votre serveur d'applications.
```sh
   @Override
    public void onNewToken(String token) {
        Log.d(TAG, "Refreshed token: " + token);

        // If you want to send messages to this application instance or
        // manage this apps subscriptions on the server side, send the
        // Instance ID token to your app server.
        sendRegistrationToServer(token);
    }

```

