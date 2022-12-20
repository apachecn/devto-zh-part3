# 动荡的一个月:Firestore 创建用户规则和测试

> 原文：<https://dev.to/abraham/a-month-of-flutter-firestore-create-user-rules-and-tests-4hf7>

*最初发表于[bendyworks.com](https://bendyworks.com/blog/a-month-of-flutter-firestore-create-user-rules-and-tests)。*

当用户[登录 Google](https://bendyworks.com/blog/a-month-of-flutter-sign-in-with-google) 时，我将在 Firestore 中创建一个用户文档。每个经过身份验证的用户应该只能创建一个用户文档。这些文档最终会被其他用户读取，所以 Firestore 需要有[服务器端验证](https://firebase.google.com/docs/firestore/security/get-started)来尽可能保持数据的正确性。

现在我正在做[注册用户](https://bendyworks.com/blog/a-month-of-flutter-user-registration-form/)，所以我只打算实现`create`规则，而不是`read`、`update`等等。

```
match /users/{userId} {
  allow create: if isOwner(userId) &&
                  // TODO: enable after bug fix https://github.com/firebase/firebase-tools/issues/1073
                  //  validCreateTimestamps() &&
                  validCreateUser();
}
~~~{% endraw %}

This will allow {% raw %}`user`{% endraw %} documents to be created if the owner has the same ID and the document being created passes validation.

Validation of timestamps is being skipped because of a [bug in the emulator](https://github.com/firebase/firebase-tools/issues/1073) that causes tests to hang. I've also been considering moving {% raw %}`createdAt` and `updatedAt` timestamps into [Cloud Functions](https://firebase.google.com/docs/functions/) but I'm not ready to make that commitment yet.

User document creation is primarily validated with two tests. One to make sure an authenticated user can create their own document and one to make sure a user can't create a document for someone else.

~~~js
@test
async 'can create self'() {
  const uid = this.user().uid;
  const user = this.db({ uid }).collection('users').doc(uid);
  await firebase.assertSucceeds(user.set(this.validUser));
}

@test
async 'can not create someone else'() {
  const user = this.db(this.user()).collection('users').doc(uuid.v4());
  await firebase.assertFails(user.set(this.validUser));
}
~~~

Additionally, there are several tests that iterate over a number of invalid values and assert they fail.

There are a number of helper methods I've defined in {% raw %}`firestore.rules`{% endraw %}:{% raw %}

~~~js
function isOwner(userId) {
  return request.auth != null &&
          request.auth.uid == userId;
}
~~~{% endraw %}

The {% raw %}`isOwner`{% endraw %} helper checks to see if the user document ID matches that of the current authenticated user.{% raw %}

~~~js
function validString(key) {
  return data()[key].trim() == data()[key] &&
          data()[key].size() > 0;
}
~~~{% endraw %}

The {% raw %}`validString`{% endraw %} helper checks to see that a required string has a value.{% raw %}

~~~js
function validUrl(url) {
  return url.matches('https://[a-zA-Z].+');
}
~~~{% endraw %}

The {% raw %}`validUrl`{% endraw %} helper checks that a string starts with {% raw %}`https://`{% endraw %}. I'm pretty sure this will not correctly validate some URLs but this value should generally be set to a Google CDN so I don't think invalid hosts will come up.{% raw %}

~~~js
function validCreateTimestamps() {
  return data().updatedAt == request.time &&
          data().createdAt == request.time;
}
~~~{% endraw %}

{% raw %}`validCreateTimestamps`{% endraw %} checks that the {% raw %}`updatedAt`{% endraw %} and {% raw %}`createdAt`{% endraw %} values match the time on the request. This works because the client will set that value with a constant that Firestore will replace with the current time.{% raw %}

~~~js
function validUser() {
  // TODO: prevent extra fields
  return validString('nickname') &&
          validString('fullName') &&
          validString('photoUrl') &&
          validUrl(data().photoUrl);
}

function validCreateUser() {
  return validUser() &&
          data().agreedToTermsAt == request.time;
}
~~~{% endraw %}

The final two helpers are to validate a user document leveraging all the other helpers. One {% raw %}`TODO`{% endraw %} I've left for the future is to make sure all fields on the document are on an [allowed list](https://github.com/abraham/birb/issues/65).

I'm not completely happy with how the {% raw %}`server`{% endraw %} tests are currently organized. I will probably do some cleanup in the future to try and make everything more elegant. I plan on adding [faker.js](https://github.com/marak/Faker.js/) for fun too.

## Code changes

- [#64 Add Firestore rules for creating users](https://github.com/abraham/birb/pull/64) 
```

Enter fullscreen mode Exit fullscreen mode