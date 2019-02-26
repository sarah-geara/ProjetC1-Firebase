# Etape 1 
[Ajoutez Firebase dans votre projet](https://firebase.google.com/docs/android/setup).

# Etape 2
- Visitez [Facebook Developers Site](https://developers.facebook.com) et faites ces
  instructions pour l'installation de votre application.
  - On vous demandera le package name, uriliser celui de votre application
  exammple : `com.google.firebase.quickstat.usermanagement`.
  - On vous demandera le nom de la main class 
  example : `com.google.firebase.quickstart.usermanagement.FacebookLoginActivity`.
- Allez sur [Firebase Console](https://console.firebase.google.com) et appuyiez sur votre projet :
  - Choisissez **Auth** panel puis  **Sign In Method** .
  - Clickez **Facebook** et sur **Enable** switch, puis **Save**.
  - Ajoutez **App Id** et **App Secret** puis **Save**.
- Ouvrir `app/src/main/res/values/ids.xml` et remplacez la valeur de `facebook_app_id` avez celui que vous venez de creer.
- Run the app on your device or emulator.
    - Select the **FacebookLoginActivity** from the main screen.
    - Click the **Sign In** button to begin.
    - If you see text that says Facebook is disabled, make sure you are running
      either the **facebookDebug** or **facebookRelease** variants of this sample.
# Etape 2
Ajoutez dans build.gradle niveau app
```sh
   // Firebase Authentication
    implementation 'com.google.firebase:firebase-core:16.0.6'
    implementation 'com.google.firebase:firebase-auth:16.1.0'
   // Facebook Android SDK (only required for Facebook Login)
   // Used in FacebookLoginActivity.
    implementation 'com.facebook.android:facebook-login:4.39.0'
    implementation 'com.android.support:customtabs:28.0.0'

```

# Etape 3
Ajoutez dans ressources --> ids.xml
```sh
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <!--
        Your Facebook App ID. See README.
    -->
    <!--  TODO(developer): REPLACE -->
    <string name="facebook_app_id">REPLACE_ME</string>

    <!--
      Your Twitter Key and Secret, see README.
    -->
    <!-- TODO(developer): REPLACE -->
    <string name="twitter_consumer_key">REPLACE_ME</string>
    <string name="twitter_consumer_secret">REPLACE_ME</string>
</resources>

```

# Etape 4
Ajoutez dans AndroidManifest.xml
```sh
  <uses-permission android:name="android.permission.INTERNET"/>
  <application>
  ....
   <!-- Facebook Configuration -->
        <meta-data
            android:name="com.facebook.sdk.ApplicationId"
            android:value="@string/facebook_app_id"
            tools:replace="android:value" />

        <activity
            android:name="com.facebook.FacebookActivity"
            android:configChanges="keyboard|keyboardHidden|screenLayout|screenSize|orientation"
            android:label="@string/app_name"
            android:theme="@android:style/Theme.Translucent.NoTitleBar"
            tools:replace="android:theme" />
   ....
 </application>
```

# Etape 5
Ajoutez dans le file layout xml de votre activity
```sh
     <com.facebook.login.widget.LoginButton
       android:id="@+id/buttonFacebookLogin"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:layout_centerInParent="true" />
```

# Etape 6
Initialisez dans votre activity
```sh
    private FirebaseAuth mAuth;
    private CallbackManager mCallbackManager;

```
Creer une fonction update ui 
    public void signOut() {
        mAuth.signOut();
        LoginManager.getInstance().logOut();

        updateUI(null);
    }

    private void updateUI(FirebaseUser user) {
        hideProgressDialog();
        if (user != null) {
            mStatusTextView.setText(getString(R.string.facebook_status_fmt, user.getDisplayName()));
            mDetailTextView.setText(getString(R.string.firebase_status_fmt, user.getUid()));

            findViewById(R.id.buttonFacebookLogin).setVisibility(View.GONE);
            findViewById(R.id.buttonFacebookSignout).setVisibility(View.VISIBLE);
        } else {
            mStatusTextView.setText(R.string.signed_out);
            mDetailTextView.setText(null);

            findViewById(R.id.buttonFacebookLogin).setVisibility(View.VISIBLE);
            findViewById(R.id.buttonFacebookSignout).setVisibility(View.GONE);
        }
    }
Dans oncreate de votre activity
```sh
       mAuth = FirebaseAuth.getInstance();

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
            }

            @Override
            public void onError(FacebookException error) {
                Log.d(TAG, "facebook:onError", error);
            
            }
        });

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
Dans onstart de votre activity
```sh
  @Override
    public void onStart() {
        super.onStart();
        // Check if user is signed in (non-null) and update UI accordingly.
        FirebaseUser currentUser = mAuth.getCurrentUser();
        updateUI(currentUser);
    }
```
