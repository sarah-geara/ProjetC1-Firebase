# Etape 1 
[Ajoutez Firebase dans votre projet](https://firebase.google.com/docs/android/setup).
      
# Etape 2
Ajoutez dans build.gradle niveau app
```sh
    implementation 'com.google.firebase:firebase-core:16.0.6'
    implementation 'com.google.firebase:firebase-analytics:16.0.6'
    
    apply plugin: 'com.google.gms.google-services'

```
# Etape 3
Initialisez dans votre activity
```sh
    // [START declare_analytics]
    private FirebaseAnalytics mFirebaseAnalytics;
    // [END declare_analytics]

```
Dans on create de votre activity
```sh
     // [START shared_app_measurement]
        // Obtain the FirebaseAnalytics instance.
        mFirebaseAnalytics = FirebaseAnalytics.getInstance(this);
        // [END shared_app_measurement]

```
# Etape 4
Pour le tracking des sections
```sh
 private void recordScreenView() {
        // This string must be <= 36 characters long in order for setCurrentScreen to succeed.
        String screenName = getCurrentImageId() + "-" + getCurrentImageTitle();

        // [START set_current_screen]
        mFirebaseAnalytics.setCurrentScreen(this, screenName, null /* class override */);
        // [END set_current_screen]
    }
 ```  
# Etape 5
Pour envoyez les events
```sh
      // [START image_view_event]
        Bundle bundle = new Bundle();
        bundle.putString(FirebaseAnalytics.Param.ITEM_ID, id);
        bundle.putString(FirebaseAnalytics.Param.ITEM_NAME, name);
        bundle.putString(FirebaseAnalytics.Param.CONTENT_TYPE, "image");
        mFirebaseAnalytics.logEvent(FirebaseAnalytics.Event.SELECT_CONTENT, bundle);
        // [END image_view_event]
```
