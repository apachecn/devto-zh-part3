# 动荡的一个月:建立 Firebase Firestore

> 原文：<https://dev.to/abraham/a-month-of-flutter-setting-up-firebase-firestore-3aem>

*最初发表于[bendyworks.com](https://bendyworks.com/blog/a-month-of-flutter-setting-up-firebase-firestore)。*

在用户[登录谷歌](https://bendyworks.com/blog/a-month-of-flutter-sign-in-with-google)和[注册](https://bendyworks.com/blog/a-month-of-flutter-user-registration-form)后，他们的信息需要被保存到数据库中。我将使用 [Firebase Firestore](https://firebase.google.com/docs/firestore/) 作为我的后端。在`birb`代码库中，我将创建一个`server`目录，并使用 [firebase-tools](https://www.npmjs.com/package/firebase-tools) 在其中初始化一个 Firestore 项目。

```
$ firebase init

     ######## #### ########  ######## ########     ###     ######  ########
     ##        ##  ##     ## ##       ##     ##  ##   ##  ##       ##
     ######    ##  ########  ######   ########  #########  ######  ######
     ##        ##  ##    ##  ##       ##     ## ##     ##       ## ##
     ##       #### ##     ## ######## ########  ##     ##  ######  ########

You're about to initialize a Firebase project in this directory:

  /home/abraham/Development/birb

? Which Firebase CLI features do you want to setup for this folder? Press Space to select features, then Enter to confirm your choices.
 ◯ Database: Deploy Firebase Realtime Database Rules
❯◉ Firestore: Deploy rules and create indexes for Firestore
 ◯ Functions: Configure and deploy Cloud Functions
 ◯ Hosting: Configure and deploy Firebase Hosting sites
 ◯ Storage: Deploy Cloud Storage security rules
~~~

I choose the [same Firebase project](https://bendyworks.com/blog/a-month-of-flutter-configure-sign-in-with-google-android) being used for authentication, the default Firestore Rules file, and the default Firestore indexes file. By default `.firebaserc` is not `.gitignored`. I have added my `.firebasrc` to `.gitignore` because this is an open source project. Anyone who forks Birb will need to set up their own Firebase project.

In the Firebase console I will now enable Firestore for the project.

![Enabling Firestore](https://thepracticaldev.s3.amazonaws.com/i/jjp5dsl68e4sdkdfg3c1.png)

Here are the default `firestore.rules` that just say don't allow reads or writes.{% raw %}

~~~js
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
~~~{% endraw %}

Deploying the rules is handled with the [firebase-tools Node package](https://www.npmjs.com/package/firebase-tools).{% raw %}

~~~bash
$ npx firebase deploy

=== Deploying to 'birb-app-dev'...

i  deploying firestore
i  firestore: checking firestore.rules for compilation errors...
i  firestore: reading indexes from firestore.indexes.json...
✔  firestore: rules file firestore.rules compiled successfully
i  firestore: uploading rules firestore.rules...
✔  firestore: deployed indexes in firestore.indexes.json successfully
✔  firestore: released rules firestore.rules to cloud.firestore

✔  Deploy complete!

Project Console: https://console.firebase.google.com/project/birb-app-dev/overview
~~~{% endraw %}

Installing the [{% raw %}`cloud_firestore`{% endraw %} package](https://pub.dartlang.org/packages/cloud_firestore) in {% raw %}`pubspec.yaml`{% endraw %} happens last.

Before integrating with the Flutter code, I'm going to write some rules with so come back soon for that article.

## Code changes

- [#57 Add Firestore](https://github.com/abraham/birb/pull/57) 
```

Enter fullscreen mode Exit fullscreen mode