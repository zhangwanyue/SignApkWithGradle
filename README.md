# configure gradle to sign apk

## ref link:

* configure gradle to sign apk: 
https://developer.android.com/studio/publish/app-signing?hl=zh-CN#gradle-sign

* Keep your key secure:
https://developer.android.com/studio/publish/app-signing?hl=zh-CN#secure-shared-keystore

## procedure

1. generate keystore

using `keytool` to generate keystore:
```
keytool -genkey -v -keystore my-release-key.keystore
-keyalg RSA -keysize 2048 -validity 10000 -alias my-alias
```

```
$ which keytool 
/usr/lib/jvm/jdk1.8.0_161/bin/keytool
```

2. configure gradle for signing and keep the key secure

1) create a `keystore.properties`

```
storeFile=xxx
storePassword=xxx
keyAlias=xxx
keyPassword=xxx
```

2) configure gradle

```groovy
// Create a variable called keystorePropertiesFile, and initialize it to your
// keystore.properties file, in the rootProject folder.
def keystorePropertiesFile = rootProject.file("app/keystore.properties")
// Initialize a new Properties() object called keystoreProperties.
def keystoreProperties = new Properties()
// Load your keystore.properties file into the keystoreProperties object.
keystoreProperties.load(new FileInputStream(keystorePropertiesFile))

android {
    ...
    signingConfigs{
        release{
            storeFile file(keystoreProperties['storeFile'])
            storePassword keystoreProperties['storePassword']
            keyAlias keystoreProperties['keyAlias']
            keyPassword keystoreProperties['keyPassword']
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
```