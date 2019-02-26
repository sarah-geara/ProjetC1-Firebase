# Etape 1 
Ajoutez Firebase dans votre projet

# Etape 2
Ajoutez dans build.gradle niveau app
```sh
implementation 'com.google.firebase:firebase-auth:16.1.0'
implementation 'com.facebook.android:facebook-android-sdk:4.34.0'

```

# Etape 3
Ajoutez dans ressources --> strings.xml
```sh
<string name="facebook_app_id">[APP_ID]</string>
```

# Etape 4
Ajoutez dans AndroidManifest.xml
```sh
  <uses-permission android:name="android.permission.INTERNET"/>
  <application>
  ....
<meta-data android:name="com.facebook.sdk.ApplicationId" 
        android:value="@string/facebook_app_id"/>
   ....
 </application>
```

# Etape 5
Ajoutez dans le file layout xml de votre activity
```sh

<com.facebook.login.widget.LoginButton
    android:id="@+id/login_button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="center_horizontal"
    android:layout_marginTop="30dp"
    android:layout_marginBottom="30dp" /> 
```

# Etape 6
Dans oncreate de votre activity
```sh
mCallbackManager = CallbackManager.Factory.create();
LoginButton loginButton = findViewById(R.id.buttonFacebookLogin);
loginButton.setReadPermissions("email", "public_profile");
loginButton.registerCallback(mCallbackManager, new FacebookCallback<LoginResult>() {
    @Override
    public void onSuccess(LoginResult loginResult) {
        Log.d(TAG, "facebook:onSuccess:" + loginResult);
        handleFacebookAccessToken(loginResult.getAccessToken());
    }

    @Override
    public void onCancel() {
        Log.d(TAG, "facebook:onCancel");
        // ...
    }

    @Override
    public void onError(FacebookException error) {
        Log.d(TAG, "facebook:onError", error);
        // ...
    }
});
// ...

```

# Etape 7
Dans onActivityResult de votre activity
```sh
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    // Pass the activity result back to the Facebook SDK
    mCallbackManager.onActivityResult(requestCode, resultCode, data);
}
```
# Etape 8
Dans onActivityResult de votre activity
```sh
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    // Pass the activity result back to the Facebook SDK
    mCallbackManager.onActivityResult(requestCode, resultCode, data);
}
```
