# Etape 1 
[Ajoutez Firebase dans votre projet](https://firebase.google.com/docs/android/setup).

# Etape 2

- Allez sur [Twitter Developers Site](https://apps.twitter.com) et clickez **Create New App**
  instructions to set up a new Android app.
  - Ensuite allez sur **Keys and Access Tokens** tab de votre app et copiez  **Consumer Key** et **Consumer Secret**.
- Allez sur [Firebase Console](https://console.firebase.google.com) et cliquez sur votre projet:
  - Choisissez  **Auth** panel et puis clickez sur **Sign In Method** tab.
  - Clickez **Twitter** et demmarez  **Enable** switch, et puis  **Save**.
  - Ajoutez votre Twitter **Consumer Key** et **Consumer Secret** et clickez sur **Save**.
  - Copiez le callback URL du font de la panel (Ex.
    `https://<your-app-id>.firebaseapp.com/__/auth/handler`).
- Naviguer retour sur votre Twitter app settings sur [Twitter Developers Site](https://apps.twitter.com). 
  - Clickez sur  **Settings** tab et
  coller le  callback URL de votre Firebase console.
- Ouvrir le file `app/src/main/res/values/ids.xml` et remplacez la valeur de  **twitter_consumer_key**
  et **witter_consumer_secret** avec la cle et secret que vous venez de mettre dans votre Firebase console.

# Etape 3
Ajoutez dans build.gradle niveau app
```sh
   // Firebase Authentication
    implementation 'com.google.firebase:firebase-core:16.0.6'
    implementation 'com.google.firebase:firebase-auth:16.1.0'
 // Twitter Android SDK (only required for Twitter Login)
    implementation 'com.twitter.sdk.android:twitter-core:3.3.0'
    implementation 'com.twitter.sdk.android:twitter:3.3.0'

```

# Etape 4
Ajoutez dans ressources --> ids.xml
```sh
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <!--
      Your Twitter Key and Secret, see README.
    -->
    <!-- TODO(developer): REPLACE -->
    <string name="twitter_consumer_key">REPLACE_ME</string>
    <string name="twitter_consumer_secret">REPLACE_ME</string>
</resources>

```

# Etape 5
Ajoutez dans le file layout xml de votre activity
```sh
   <com.twitter.sdk.android.core.identity.TwitterLoginButton
            android:id="@+id/buttonTwitterLogin"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"/>

        <Button
            android:id="@+id/buttonTwitterSignout"
            style="@style/Widget.AppCompat.Button.Colored"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="@string/sign_out"
            android:theme="@style/ThemeOverlay.MyDarkButton"
            android:visibility="gone"/>
```

# Etape 7
Initialisez dans votre activity
```sh
   // [START declare_auth]
    private FirebaseAuth mAuth;
    // [END declare_auth]

    private TwitterLoginButton mLoginButton;
```
ajoutez dans on create de votre activity
```sh
     // Configure Twitter SDK
        TwitterAuthConfig authConfig =  new TwitterAuthConfig(
                getString(R.string.twitter_consumer_key),
                getString(R.string.twitter_consumer_secret));

        TwitterConfig twitterConfig = new TwitterConfig.Builder(this)
                .twitterAuthConfig(authConfig)
                .build();

        Twitter.initialize(twitterConfig);

        // Inflate layout (must be done after Twitter is configured)
        setContentView(R.layout.activity_twitter);

        // Views
        mStatusTextView = findViewById(R.id.status);
        mDetailTextView = findViewById(R.id.detail);
        findViewById(R.id.buttonTwitterSignout).setOnClickListener(this);

        // [START initialize_auth]
        // Initialize Firebase Auth
        mAuth = FirebaseAuth.getInstance();
        // [END initialize_auth]

        // [START initialize_twitter_login]
        mLoginButton = findViewById(R.id.buttonTwitterLogin);
        mLoginButton.setCallback(new Callback<TwitterSession>() {
            @Override
            public void success(Result<TwitterSession> result) {
                Log.d(TAG, "twitterLogin:success" + result);
                handleTwitterSession(result.data);
            }

            @Override
            public void failure(TwitterException exception) {
                Log.w(TAG, "twitterLogin:failure", exception);
                updateUI(null);
            }
        });
        // [END initialize_twitter_login]
```

# Etape 8
Dans onActivityResult de votre activity
```sh
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
     // Pass the activity result to the Twitter login button.
        mLoginButton.onActivityResult(requestCode, resultCode, data);
    }
```
# Etape 9
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
# Etape 10
Enfin ajoutez ces autres fonctions pour le traitement du user interface
```sh
  // [START auth_with_twitter]
    private void handleTwitterSession(TwitterSession session) {
        Log.d(TAG, "handleTwitterSession:" + session);
        // [START_EXCLUDE silent]
        showProgressDialog();
        // [END_EXCLUDE]

        AuthCredential credential = TwitterAuthProvider.getCredential(
                session.getAuthToken().token,
                session.getAuthToken().secret);

        mAuth.signInWithCredential(credential)
                .addOnCompleteListener(this, new OnCompleteListener<AuthResult>() {
                    @Override
                    public void onComplete(@NonNull Task<AuthResult> task) {
                        if (task.isSuccessful()) {
                            // Sign in success, update UI with the signed-in user's information
                            Log.d(TAG, "signInWithCredential:success");
                            FirebaseUser user = mAuth.getCurrentUser();
                            updateUI(user);
                        } else {
                            // If sign in fails, display a message to the user.
                            Log.w(TAG, "signInWithCredential:failure", task.getException());
                            Toast.makeText(TwitterLoginActivity.this, "Authentication failed.",
                                    Toast.LENGTH_SHORT).show();
                            updateUI(null);
                        }

                        // [START_EXCLUDE]
                        hideProgressDialog();
                        // [END_EXCLUDE]
                    }
                });
    }
    // [END auth_with_twitter]

    private void signOut() {
        mAuth.signOut();
        TwitterCore.getInstance().getSessionManager().clearActiveSession();

        updateUI(null);
    }

    private void updateUI(FirebaseUser user) {
        hideProgressDialog();
        if (user != null) {
            mStatusTextView.setText(getString(R.string.twitter_status_fmt, user.getDisplayName()));
            mDetailTextView.setText(getString(R.string.firebase_status_fmt, user.getUid()));

            findViewById(R.id.buttonTwitterLogin).setVisibility(View.GONE);
            findViewById(R.id.buttonTwitterSignout).setVisibility(View.VISIBLE);
        } else {
            mStatusTextView.setText(R.string.signed_out);
            mDetailTextView.setText(null);

            findViewById(R.id.buttonTwitterLogin).setVisibility(View.VISIBLE);
            findViewById(R.id.buttonTwitterSignout).setVisibility(View.GONE);
        }
    }

    @Override
    public void onClick(View v) {
        int i = v.getId();
        if (i == R.id.buttonTwitterSignout) {
            signOut();
        }
    }
```


