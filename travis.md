To trigger build
```
 git commit -m "on tag 1.6 commit"
 git tag 1.6
 git push origin 1.6 // If in deploy section is mentioned only on tag then this will update the release
 git push origin master // If not then normal branch push will work
 
 And let the tag build to finish on travis it will overrite the previously pushed tag
```

Add this as travis file(.travis.yml) to root folder of android project to build it.
```
language: android
sudo: required
#os: [linux]
dist: trusty

env:
  global:
    - ANDROID_API=29
    - ANDROID_BUILD_TOOLS_VERSION=29.0.3
    - EMULATOR_API=29
    - EMULATOR_ABI=armeabi-v7a

android:
  components:
    - platform-tools
    - tools
    - build-tools-$ANDROID_BUILD_TOOLS_VERSION
    - android-$ANDROID_API
    - extra-google-google_play_services
    - extra-google-m2repository
    - extra-android-m2repository
    - addon-google_apis-google-$ANDROID_API
    - extra-android-support
    - sys-img-x86-android-$ANDROID_API


before_install:
  - yes | sdkmanager "platforms;android-$ANDROID_API"

script:
  - ./gradlew assembleDebug

deploy:
  provider: releases
  api-key: $GITHUB_API_KEY
  file: $TRAVIS_BUILD_DIR/app/build/outputs/apk/debug/app-1.0-debug.apk
  skip_cleanup: true
  name: dev-build-$TRAVIS_TAG
  body: Automatic build of $TRAVIS_BRANCH ($TRAVIS_COMMIT) built by Travis CI on $(date +'%F %T %Z').
  prerelease: true
  overwrite: true
  target_commitish: $TRAVIS_COMMIT
  on:
    tags: true

after_deploy:
  - rm -rf $TRAVIS_BUILD_DIR/app/build/outputs
```
