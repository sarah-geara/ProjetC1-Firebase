# Firebase Authentication

FirebaseUI est une librarie construite sur le SDK d’authentification Firebase qui fournit des flux d’interface utilisateur intégrés à utiliser dans votre application. FirebaseUI offre les avantages suivants:

Plusieurs fournisseurs : les flux de connexion pour les adresses de messagerie / mot de passe, lien de messagerie, authentification du téléphone, connexion à Google, connexion à Facebook, à Twitter et à GitHub.
Gestion de compte : flux permettant de gérer les tâches de gestion de compte, telles que la création de compte et la réinitialisation de mots de passe
Liaison de compte : flux permettant de lier en toute sécurité des comptes d'utilisateurs à des fournisseurs d'identité.
Mise à niveau d'utilisateur anonyme : flux permettant de mettre à niveau en toute sécurité des utilisateurs anonymes.
Thèmes personnalisés: personnalisez l'apparence de FirebaseUI en fonction de votre application. De plus, comme FirebaseUI est open source, vous pouvez créer un projet et le personnaliser exactement selon vos besoins.
Smart Lock for Passwords : Intégration automatique avec Smart Lock for Passwords pour une connexion rapide entre appareils.

## Connexions

Dans ce tutorat nous allons donner exemple sur le platforme Android

* [Facebook Login](FacebookLogin)
* [Google SignIn](GoogleSignIn)
* [Twittre Login](TwitterLogin)


### Etape 1:

Ajoutez dans build.gradle au niveau app les implementations suivantes : 

```markdown
dependencies {
    // ...

    implementation 'com.firebaseui:firebase-ui-auth:4.3.1'

    // Required only if Facebook login support is required
    // Find the latest Facebook SDK releases here: https://goo.gl/Ce5L94
    implementation 'com.facebook.android:facebook-android-sdk:4.x'

    // Required only if Twitter login support is required
    // Find the latest Twitter SDK releases here: https://goo.gl/E5wZvQ
    implementation 'com.twitter.sdk.android:twitter-core:3.x'
}

```
### Etape 2:

Ajoutez dans votre file strings.xml les ids

```markdown

<resources>
  <!-- Facebook application ID and custom URL scheme (app ID prefixed by 'fb'). -->
  <string name="facebook_application_id" translatable="false">YOUR_APP_ID</string>
  <string name="facebook_login_protocol_scheme" translatable="false">fbYOUR_APP_ID</string>
  <!-- Twitter consumer key and secret -->
  <string name="twitter_consumer_key" translatable="false">YOUR_CONSUMER_KEY</string>
  <string name="twitter_consumer_secret" translatable="false">YOUR_CONSUMER_SECRET</string>
</resources>

```

### Etape 3:

Ajoutez dans votre MainActivity.activity

```markdown

```


