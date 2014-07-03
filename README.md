# QuickBlox
QuickBlox - Communication backed as a Service (CaaS) platform which brings superpowers to mobile apps.

QuickBlox is a suite of communication features & data services (APIs, SDKs, code samples, admin panel, tutorials) which help digital agencies, mobile developers and publishers to add great functionality to smartphone applications.

Please read full Android SDK documentation on the [QuickBlox website, Android section](http://quickblox.com/developers/Android)

# Q-municate 
Q-municate is an open source code of chat application with full range of communication features on board (such as messaging, file transfer, push notifications, audio/video calls).

We are inspired to give you chat application out of the box. You can customize this application depending of your needs.
As always QuickBlox backend is at your service: http://quickblox.com/plans/

Find the source code and more information about Q-municate in our Developers section: http://quickblox.com/developers/q-municate


# Q-municate Android
This guide is created by QuickBlox Android team to explain how you can build a communication app on Android with Quickblox API.

It is a step by step guide designed for all developer levels including beginners as we move from simple to more complex implementation. Depending on your skills and your project requirements you may choose which parts of this guide are to follow. Enjoy and please get in touch, if you need assistance from QuickBlox Android team.

Q-municate is a fully fledged chat application using the Quickblox API.

## Q-municate application uses following QuickBlox modules:
* [Chat (v 2.0.)](http://quickblox.com/modules/chat)
* [Users](http://quickblox.com/modules/users/)
* [Content](http://quickblox.com/modules/content/) 
* [Custom objects](http://quickblox.com/modules/custom/)
* [Messages](http://quickblox.com/modules/messages)

## It includes such features as:

* Two sign-up methods – Facebook and with email/password
* Login using Facebook account and email/password
* Auto search and import of user’s friends with QM accounts with help of friend’s Facebook credentials and email (for the first login)
* Invite Facebook and QB friends from database (list view)
* View Friends list
* Settings (edit users profile, reset password, logout) 
* Audio calling (Web RTC)
* Video calling (Web RTC)
* Create a private/group chat
* Participate in Private Chat
* Participate in Group Chat
* View list of all active chats with chat history (private chats and group chats)
* View and edit group chat info (title, logo, add friend or leave a group chat
* Allow users to edit their profile (set their own avatar and status (short text message))

Please note all these featureas are available in open source code, so you can customize your app depending on your needs.

## Software Environment
* The Android application runs on the phones with screen sizes varying between 3 and 5 inches, with Android 4 and above till Android 4.4 onboard.
* The Android App is developed as native Android application.
* Web component is based on QuickBlox platform.
* The App and Web panel has English language interface.
* The App works only in Portrait screen mode

## Step by step guide
### Step 1. PreLogin page
![PreLogin page](http://files.quickblox.com/Screenshot_2014-07-03-10-22-18.png) &nbsp;

#### Available features:
#### Buttons:
* Connect with FB– this button allows user to enter the App with his/her Facebook credentials, if user has entered Facebook credentials into device settings.
* If there are no Facebook credentials in the device’s settings, App shows pop-up message with appropriate text. After pop-up message Facebook authorization page will be shown .
* If App has passed Facebook authorization successfully, the App will show pop-up message 
* Sign up (with email) – if tapped, user is redirected to Register Page 
* Already have an account? (Log in)– button allows user to enter the App if he/she provides correct and valid email and password. By tapping on this button user will be redirected to the login screen.

###### Please note, that user will skip this page, if “Remember me” tick is set in the check box on Login page.

### Step 2. SignUp page
![SignUp page](http://files.quickblox.com/Screenshot_2014-07-03-10-22-30.png) &nbsp;

Sign Up Page allows to create new QM user.

#### Available features:
#### Fields set:
* Full name – text/numeric fields 3 chars min and 50 chars max
02.07.14	
(should contain alphanumeric and space characters only), mandatory
* Email – text/numeric/symbolic fields 3 chars min - no border, mandatory (email symbols validation included)
* Password – text/numeric/symbolic field 8-40 chars (should contain alphanumeric and punctuation characters only) , mandatory
* Choose user picture avatar icon- will be auto filled with selected image, if image is chosen.

#### Buttons:
* Choose user picture – all area and button is tappable/ clickable. After tap/click will be opened a gallery with images to choose, not mandatory. App will create round image from the center part of the selected image automatically.
* Sign up – if all fields are filled in correctly, then user is navigated to Friends Page.
Data validation will be done on the server. (Validation process is the same as for Login page) 
* Back button - redirects user to Prelogin page

When new user is registered in the system , Facebook and email import friends will be done.
Remember me tick in the check box on Login page will be set automatically, so there is no need for user to enter credentials during the next login.

#### Please find below how you can do it in code:

```java
public QBUser signup(QBUser inputUser, File file) throws QBResponseException, BaseServiceException {
  QBUser user;
  QBAuth.createSession();
  String password = inputUser.getPassword();
  inputUser.setOldPassword(password);
  user = QBUsers.signUpSignInTask(inputUser);
  if (null != file) {
    QBFile qbFile = QBContent.uploadFileTask(file, true, (String) null);
    user.setWebsite(qbFile.getPublicUrl());
    user = QBUsers.updateUser(inputUser);
  }
  user.setPassword(password);
  String token = QBAuth.getBaseService().getToken();
  AppSession.startSession(LoginType.EMAIL, user, token);
  return inputUser;
}
```

### Step 3. LogIn page
![LogIn page](http://files.quickblox.com/Screenshot_2014-07-03-10-22-42.png) &nbsp;

User can login in the app via Facebook or login as a QuickBlox user.

#### 3.1. Connect with Facebook

By tapping on Connect with Facebook button app will take user’s Facebook credentials ( from device settings) and automatically create QM account for a user. 

If user signed up with Facebook, for user’s profile will be used FB avatar image, full name and email (user can’t edit email, because it is used as FB identifier)

#### Please find below how you can do it in code:

```java
public QBUser login(String socialProvider, String accessToken,
  String accessTokenSecret) throws QBResponseException, BaseServiceException {
  QBUser user;
  QBSession session = QBAuth.createSession();
  user = QBUsers.signInUsingSocialProvider(socialProvider, accessToken, accessTokenSecret);
  user.setPassword(session.getToken());
  String token = QBAuth.getBaseService().getToken();
  AppSession.startSession(LoginType.FACEBOOK, user, token);
  return user;
}
```

#### 3.2. LogIn as QuickBlox User
#### Available features:

#### Fields set :
* Email – text/numeric/symbolic fields 3 chars min - no border, mandatory (email symbols validation included)
User should be able to paste his/her email address in this field if it is currently in clipboard

* Password – text/numeric/symbolic field 8-40 chars (should contain alphanumeric and punctuation characters only) , mandatory
Input symbols are replaced with * ,so that nobody could steal user's password
User should be able to paste his/her password in this field if it is currently in clipboard

* Remember me – check box, default = 1. Allows user to save his login data so that he/she doesn't have to enter them again on the next work session start. If this checkbox is set to 1, then login and password are filled in the input fields each time he/she returns in the App, even if it was compulsory stopped earlier. Login Page is shown again if user tapped Log Out in Side Bar.
User can tap in the input field and edit login or password to enter app from another account.
***Email and Password fields have place holders as depicted on
fig.

#### Buttons:
* Connect with FB– this button allows user to enter the App with Facebook credentials set in the device settings. (same functionality as for Connect with Facebook button on Prelogin screen)
* Log in– these button allows user to enter the App if he/she provides correct and valid email and password.
If user provides incorrect/invalid login credentials (email and password), the App shows pop-up with alert message. Alert message will be sent from the server, so app just needs to show it.
Once user provides valid login credentials and taps on “Logint” button, App will search for user’s friends in the list of existing Q-municate users (by Facebook id and email).
All friends will be imported into Friends page list.
User will be redirected to Friends screen (Main Page).
Data validation will be done on the server.
Tapping on Forgot password link a predefined email from the server will be sent which will include restore password link.
Back button – if tapped, user is redirected to Prelogin Page 

![Forgot password page](http://files.quickblox.com/Screenshot_2014-07-03-10-22-53.png) &nbsp;

#### Please find below how you can do it in code:

```java
public QBUser login(QBUser inputUser) throws QBResponseException, BaseServiceException {
  QBUser user;
  QBAuth.createSession();
  String password = inputUser.getPassword();
  user = QBUsers.signIn(inputUser);
  String token = QBAuth.getBaseService().getToken();
  user.setPassword(password);
  AppSession.startSession(LoginType.EMAIL, user, token);
  return user;
}
```

### Step 4. Import friends feature.

An app will import all user’s friends by email and Facebook ID after the first app login.

#### Feature work flow:
Notification with text "Please wait, Qmunicate app is searching for your friends" and spiner should be shown. Tapping out of the notification (or OK button) user can close this pop-up. App takes all emails from the phone contacts list and search them in Qmunicate users table (in the background). Adds all Qmunicate friends on the Friends page, If there are any in the search result. Friends screen will be shown. On Friends screen will be shown grey text “Invite your friends”, if there are no friends in the friends list.

#### Please find below how you can do it in code:

```java
// getting Q-municate users by existing facebook ids
realFriendsFacebookList = QBUsers.getUsersByFacebookId(friendsFacebookList, requestBuilder, params);
…
// getting Q-municate users by existing e-mails
realFriendsContactsList = QBUsers.getUsersByEmails(friendsContactsList, requestBuilder, params);
…

// inviting users 
public void inviteFriend(int userId) throws Exception {
  if (isNotInvited(userId)) {
    sendInvitation(userId);
    addUserToFriendlist(userId);
  }
}
```

### Step 5. Friends page
![Friends page](http://files.quickblox.com/Screenshot_2014-07-03-10-48-30.png) &nbsp;

User goes to Friends page, if correct credentials are entered
Friends Page is used for list of user’s friends.

#### Friends page available features:
* All friend’s contacts (online/offline/pending contact request) in alphabetical order
For each contact will be shown full name, avatar image, short text message (status) or last activity and online/offline status. For offline status there is no special icon ( just no green dot). For Pending contact request status there is no special icon - only grey text.
* Contacts are shown as a scrollable table view
* User can tap any contact to open Friend’s profile page.
* Search icon in top right corner opens/hides search bar.

Search bar is shown on top of the contacts list

Side bar will be shown during first app login. 

#### Please find below how you can do it in code:

```java
// updating Friends list
public List<Integer> updateFriendList() throws QBResponseException {
  List<Integer> userIds = getUserIdsFromRoster();
  updateFriends(userIds);
  return userIds;
}

// invating User to list
public void inviteFriend(int userId) throws Exception {
  if (isNotInvited(userId)) {
    sendInvitation(userId);
    addUserToFriendlist(userId);
  }
}
```

#### Step 5.1. Side bar
![Side bar](http://files.quickblox.com/Screenshot_2014-07-03-10-46-33.png) &nbsp;

* Friends page (Main page): 
- A list of friends, listed in alphabetical order.

* Chats page:
- A list of chat messages, listed by date. The last one message should be shown on top.
- A badge counter will be shown on the Chats tab, if there are unread chats or missed calls.

* Invite friends page:
- A page with Facebook and email contacts, who can be invited/emailed with predefined text .

* Settings page:
- A page with app settings and preferences.

#### Step 5.2. Search Bar
![Search Bar](http://files.quickblox.com/Screenshot_2014-07-03-11-18-26.png) &nbsp;
Search icon on Friends page opens/hides search bar.

#### Available features:
A list of friends, listed in alphabetical order.

#### Please find below how you can do it in code:

```java
…
// searching Q-municate users by fullname
List<QBUser> userList = QBUsers.getUsersByFullName(constraint, requestBuilder, requestParams);
…
```

### Step 6. Details Page
![Details Page](http://files.quickblox.com/Screenshot_2014-07-03-10-48-51.png) &nbsp;

Details Page is used for friends profile information.

#### Available features:
Friends profile page shows user’s information:
- Full name  
- Short text message with text/numeric fields 128 chars max, not mandatory
- Status  (online/offline)
- Email 
- Mobile phone number  /numeric fields, not mandatory

#### Buttons:
##### Video call:
- Video call button starts video call with current user 

##### Audio call:
- Audio call button starts audio call with current user

##### Chat:
- Chat button starts chat with current user 

##### Remove contact:
- Remove contact- delete current user from the Friends list

##### Back:
- Back button returns to the previous screen (Main page)
 
### Step 7. Invite Friends
![Invite Friends](http://files.quickblox.com/Screenshot_2014-07-03-10-46-06.png) &nbsp;

User can access Invite Friends page from the Side bar, to invitу his/her friends in the app.

#### Invite Friends Page features:
* Folding friends list from Facebook
* Folding friends list from Contacts
* Scrollable friends list
* Scrollable contacts list
* Check box beside each contact full name, to be able to add needed user(s) to the Friends list.
* Next button adds selected friends to the Friends Page

#### If Fecabook friends are selected- Fig.4.3-2 – Fecebook access pop-up message will appear.
* Back button returns user to the Friends page

#### Please find below how you can do it in code:

```java
// send invite by e-mail
public static void sendInviteEmail(Context context, String[] selectedFriends) {
  Resources resources = context.getResources();
  Intent intentEmail = new Intent(Intent.ACTION_SEND);
  intentEmail.putExtra(Intent.EXTRA_EMAIL, selectedFriends);
  intentEmail.putExtra(Intent.EXTRA_SUBJECT, resources.getText(R.string.inf_subject_of_invitation));
  intentEmail.putExtra(Intent.EXTRA_TEXT, resources.getText(R.string.inf_body_of_invitation));
  intentEmail.setType(Consts.TYPE_OF_EMAIL);
  context.startActivity(Intent.createChooser(intentEmail, resources.getText(R.string.inf_choose_email_provider)));
}

// send invite to Facebook wall
public void postInviteToWall(Request.Callback requestCallback, String[] selectedFriends) {
  Session session = Session.getActiveSession();
  if (session != null) {
    Resources resources = activity.getResources();
    Bundle postParams = new Bundle();
    postParams.putString(Consts.FB_WALL_PARAM_NAME, resources.getString(R.string.inf_fb_wall_param_name));
    postParams.putString(Consts.FB_WALL_PARAM_DESCRIPTION, resources.getString(R.string.inf_fb_wall_param_description));
    postParams.putString(Consts.FB_WALL_PARAM_LINK, resources.getString(R.string.inf_fb_wall_param_link));
    postParams.putString(Consts.FB_WALL_PARAM_PICTURE, resources.getString(R.string.inf_fb_wall_param_picture));
    postParams.putString(Consts.FB_WALL_PARAM_PLACE, resources.getString(R.string.inf_fb_wall_param_place));
    postParams.putString(Consts.FB_WALL_PARAM_TAGS, TextUtils.join(",", selectedFriends));
    Request request = new Request(session, Consts.FB_WALL_PARAM_FEED, postParams, HttpMethod.POST, requestCallback);
    RequestAsyncTask task = new RequestAsyncTask(request);
    task.execute();
  }
}
```

### Step 8. Chats page
![Chats page](http://files.quickblox.com/Screenshot_2014-07-03-10-42-41.png) &nbsp; 

Chats Page shows scrollable chats list (private and group).

#### Available features:
* A list of current chats, listed by date. The last message should be shown on top.
* Plus icon opens New chat page 
* Chat  information:
- Full name / group name
- Status- short text message with text/numeric fields 128 chars max, not mandatory
- Icon , not mandatory (or grey place holder image)
- Blue badge counter shows number of participants in group chat
- A red badge counters will be shown on the Chats Page beside chat’s name, if there are some unread messages. Unread message will be marked as read, when user enters the chat  with unread message.
- User can enter group chart or private chat to read/write chat messages or find more information about chat.

#### Please find below how you can do it in code:

```java
// loading Dialogs
public List<QBDialog> getDialogs() throws QBResponseException, XMPPException, SmackException {
 Bundle bundle = new Bundle();
 QBCustomObjectRequestBuilder customObjectRequestBuilder = new QBCustomObjectRequestBuilder();
 customObjectRequestBuilder.setPagesLimit(Consts.CHATS_DIALOGS_PER_PAGE);
 List<QBDialog> chatDialogsList = QBChatService.getChatDialogs(null, customObjectRequestBuilder, bundle);
 return chatDialogsList;
}
```

### Step 8. New chat page
![New chat page](http://files.quickblox.com/Screenshot_2014-07-03-10-37-55.png) &nbsp; 

New Chat Page allows to create new chat.

#### Available features:
* A list of friends, listed in alphabetical order.
* Tick/ create chat button in top left and right corners create group chat with selected friends. Create Private Chat button will be shown, if  at least one user is selected.
* Back button returns to the Chats Page (chapter 4.9. Fig.4.9-1, Fig.4.4-2 )
* In the right side of the screen there is a row with check boxes for selected friends.

#### Please find below how you can do it in code:

```java
// creating Chat Room
public QBDialog createRoomChat(String roomName,
            List<Integer> friendIdsList) throws SmackException, XMPPException, QBResponseException {
  ArrayList<Integer> occupantIdsList = ChatUtils.getOccupantIdsWithUser(friendIdsList);
  QBDialog dialog = roomChatManager.createDialog(roomName, QBDialogType.GROUP, occupantIdsList);
  joinRoomChat(dialog);
  inviteFriendsToRoom(dialog, friendIdsList);
  saveDialogToCache(context, dialog);
  return dialog;
}
```
### Step 9. Private chat page
![Private chat page](http://files.quickblox.com/Screenshot_2014-07-03-10-44-20.png) &nbsp; 

Private Chat Page is used for messaging with a friend.

#### Private Chat Page features:
* This page is a chat room for 1x1 chat
* User types his message and sends it to chatroom – it appears on the left side of the screen in one color.
* Friend’s messages will be shown in the right corner in another color.
* Tapping on friend’s avatar or name opens Action Menu. (Chapter 4.9.4. Fig. 4.9.4-1)
* Data set:
- Page header:
Friend’s full name
Status – shows opponent’s network status:
Online (green dot beside user’s name)
- Message:
photo – shows chat opponent’s and user’s user-pictures
Message text
Timestamp – device time and date should be used

* Buttons set:
- VideoChat – starts videochat with current chat opponent
- AudioChat- – starts audiochat with current chat opponent
- Attachment menu – allows to add an attachment to a message (fig.4.9-2)
- :) icon- opens Emoticons Tab
- Send – sends whatever is entered in Message field
- Message field – text/numeric/symbolic field 
- Back button returns to the Chats page 

#### Please find below how you can do it in code:

```java
// creating Private Chat
public QBDialog createPrivateChatOnRest(int opponentId) throws QBResponseException {
  QBDialog dialog = privateChatManager.createDialog(opponentId);
  saveDialogToCache(context, dialog);
  try {
    notifyFriendCreatedPrivateChat(dialog, opponentId);
  } catch (Exception e) {
    ErrorUtils.logError(e);
  }
  this.opponentId = opponentId;
  return dialog;
}

// sending Private Message
private void sendPrivateMessage(QBFile file, String message, int userId) throws QBResponseException {
  QBChatMessage chatMessage;
  if (file != null) {
    chatMessage = getQBChatMessageWithImage(file);
  } else {
    chatMessage = getQBChatMessage(message);
  }
  String dialogId = null;
  if (currentDialog != null) {
    dialogId = currentDialog.getDialogId();
  }
  sendPrivateMessage(chatMessage, userId, dialogId);
  String attachUrl = file != null ? file.getPublicUrl() : Consts.EMPTY_STRING;
  long time = Long.parseLong(chatMessage.getProperty(PROPERTY_DATE_SENT).toString());
  if (dialogId != null) {
    saveMessageToCache(new DialogMessageCache(dialogId, chatCreator.getId(), chatMessage.getBody(),
    attachUrl, time, true));
  }
}

// sending Private Message With Attach Image
public void sendPrivateMessageWithAttachImage(QBFile file, int userId) throws QBResponseException {
  sendPrivateMessage(file, null, userId);
}
```

### Step 10. Group chat page
![Group chat page](http://files.quickblox.com/Screenshot_2014-07-03-10-40-23.png) &nbsp; 

Group Chat Page is used for messaging with friends.

#### Group Chat Page features:

* This page is a chat room for multiple chat users
* User types his message and sends it to chatroom – it appears on the left side of the screen in one color.
* Friends messages will be shown in the right corner in another color.
* Data set:
- Page header:
Group chat name
Total number of users/ number of online users
- Message:
photo – shows chat opponent’s and users user-pictures
Message text
Timestamp – device time and date should be used
* Buttons set:
- Info icon (in the top right corner)- opens Group Chat Details page
- Send – sends whatever is entered in Message field
- Tapping on any friend’s name opens Action pop-up
* Message field – text/numeric/symbolic field 512 chars max
* Back button returns to the Chats page (chapter 4.6. Fig.4.6-1)

#### Please find below how you can do it in code:

```java
// creating Group Chat
public QBDialog createRoomChat(String roomName, List<Integer> friendIdsList) throws SmackException, XMPPException, QBResponseException {
  ArrayList<Integer> occupantIdsList = ChatUtils.getOccupantIdsWithUser(friendIdsList);
  QBDialog dialog = roomChatManager.createDialog(roomName, QBDialogType.GROUP, occupantIdsList);
  joinRoomChat(dialog);
  inviteFriendsToRoom(dialog, friendIdsList);
  saveDialogToCache(context, dialog);
  return dialog;
}

// sending Group Message
private void sendRoomMessage(QBChatMessage chatMessage, String roomJId,
            String dialogId) throws QBResponseException {
  roomChat = roomChatManager.getRoom(roomJId);
  if (roomChat == null) {
    return;
  }
  String error = null;
  if (!TextUtils.isEmpty(dialogId)) {
    chatMessage.setProperty(ChatUtils.PROPERTY_DIALOG_ID, dialogId);
  }
  try {
    roomChat.sendMessage(chatMessage);
  } catch (XMPPException e) {
    error = context.getString(R.string.dlg_fail_send_msg);
  } catch (SmackException.NotConnectedException e) {
    error = context.getString(R.string.dlg_fail_connection);
  }
  if (error != null) {
    throw new QBResponseException(error);
  }
}

// sending Group Message With Attach Image
public void sendGroupMessageWithAttachImage(String roomJidId, QBFile file) throws QBResponseException {
        QBChatMessage chatMessage = getQBChatMessageWithImage(file);
  sendRoomMessage(chatMessage, roomJidId, currentDialog.getDialogId());
}
```

### Step 11. Calls
### Audio call
![Audio call page](http://files.quickblox.com/Screenshot_2014-07-03-11-02-37.png) &nbsp; 

#### Audio Call Page features:
* This page is shown once user initiates an audio call
* Video Call buttons:
- Mute sound – disables user’s device speaker
Can be enabled by tapping it once more
- Mute voice – disables user’s device microphone
Can be enabled by tapping it once more
- End call – ends current call and redirects user to 1x1 Chat Page
* Main page area shows user’s chat opponent avatar, full name and duration of a call

#### Please find below how you can do it in code:

```java
// making audio call to User
private void callToUser(Friend friend, WebRTC.MEDIA_STREAM callType) {
  if (friend.getId() != AppSession.getSession().getUser().getId()) {
    CallActivity.start(FriendDetailsActivity.this, friend, callType);
  }
}

*** WebRTC.MEDIA_STREAM callType - type of call (WebRTC.MEDIA_STREAM.VIDEO or WebRTC.MEDIA_STREAM.AUDIO)
```

### Video call
![Video call page](http://files.quickblox.com/Screenshot_2014-07-03-11-02-14.png) &nbsp; 

### Video Chat Page features:
* This page is shown once user initiates a video call
* Video Chat buttons:
- Change camera – allows user to change back camera to front camera and vice versa
- Mute voice – disables user’s device microphone
Can be enabled by tapping it once more
- End call – ends current call and redirects user to Main Page
* Main page area shows user’s chat opponent, small rectangle area in bottom left part of screen shows user (as shown on 4.14-1)

#### Please find below how you can do it in code:

```java
// making audio call to User
private void callToUser(Friend friend, WebRTC.MEDIA_STREAM callType) {
  if (friend.getId() != AppSession.getSession().getUser().getId()) {
    CallActivity.start(FriendDetailsActivity.this, friend, callType);
  }
}

*** WebRTC.MEDIA_STREAM callType - type of call (WebRTC.MEDIA_STREAM.VIDEO or WebRTC.MEDIA_STREAM.AUDIO)
```

### Step 12. Settings Page
![Settings page](http://files.quickblox.com/Screenshot_2014-07-03-10-46-47.png) &nbsp;

Settings Page allows user to change his/her profile and change other inapp controls.

#### Buttons set:
* Profile
- Profile available controls:
- User full name – editable field
- Avatar- user picture- editable
- Avatar – if tapped, it allows to select user’s photo from local storage
Email –editable field
- Status- editable (short text message)
* Push notifications ON/OFF – this switch allows user either to enable or disable push messages which notify about new stories appearing in My Friends Stories section of the Application
* Change password
- Password, password confirmation input fields and Apply button.
* Log Out – logs current user out from the Application and redirects him/her to Login Page
* Back button navigates user back to Home Page
* Possibility to change presence status will be excluded from the settings screen.

### Step 13. Profile Page
![Profile page](http://files.quickblox.com/Screenshot_2014-07-03-10-47-43.png) &nbsp;

Profile page allows user to edit his/her profile info.

#### Available features:

#### Fields set:
* Full name – text/numeric fields 128 chars max
* Email – text/numeric/symbolic fields 128 chars max
* User picture name field will be auto filled with selected image name, if image is chosen.
* tatus – text/numeric/symbolic field 256 chars max

#### Buttons:
* User picture – all area and button is tappable/ clickable. After tap/click will be opened a gallery with images to choose, not mandatory.
App will create round image from the center part of the selected image automatically.
* Change/ Edit status – if tapped a typing indicator in the input field will appear
* Back button- tapping on back button user confirms current profile information (changes)

#### Please find below how you can do it in code:

```java
// updating users
public QBUser updateUser(QBUser user, File file) throws QBResponseException {
  QBFile qbFile = QBContent.uploadFileTask(file, true, (String) null);
  user.setWebsite(qbFile.getPublicUrl());
  user.setFileId(qbFile.getId());
  return updateUser(user);
}
```

### Important - how to build your own Chat app</h3>

If you want to build your own iOS app using ChattAR as a basis, please do the following:<br />
1) download the project from here (GIT)<br />
2) register a QuickBlox account (if you don't have one yet): http://admin.quickblox.com/register<br />
3) log in to QuickBlox admin panel [http://admin.quickblox.com/signin]http://admin.quickblox.com/signin<br />
4) create a new app <br />
5) click on the app title in the list to reveal app details:<br />

![App credentials](http://files.quickblox.com/app_credentials.png)

6) copy credentials (App ID, Authorization key, Authorization secret) into your Q-municate project code in Consts.java<br />
7) Enjoy!
