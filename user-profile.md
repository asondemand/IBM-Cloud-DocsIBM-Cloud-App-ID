---

copyright:
  years: 2017, 2018
lastupdated: "2018-02-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:pre: .pre}


# Accessing user attributes
{: #user-profile}

A user attribute is a segment of information in an entity that is stored and maintained by {{site.data.keyword.appid_full}}. The profile contains a user's attributes and identity that is managed by an identity provider or it can be anonymous. You can use the profiles to create personalized experiences of your app for each user.
{:shortdesc}


{{site.data.keyword.appid_short_notm}} provides an API for logging in, either anonymously or by authenticating with an OpenId Connect (OIDC) [identity providers](/docs/services/appid/identity-providers.html). The user profile attribute API endpoint is a resource that is protected by the access token that is generated by {{site.data.keyword.appid_short_notm}} during the login and authorization process.


## Storing, reading, and deleting user attributes
{: #storing-data}

{{site.data.keyword.appid_short_notm}} provides a <a href="https://appid-profiles.ng.bluemix.net/swagger-ui/index.html#/Attributes" target="_blank">REST API <img src="../../icons/launch-glyph.svg" alt="External link icon"></a> for performing create, retrieve, update, and delete operations on user's attributes. The service also provides an SDK for <a href="https://github.com/ibm-cloud-security/appid-clientsdk-android" target="_blank">Android <img src="../../icons/launch-glyph.svg" alt="External link icon"></a> and <a href="https://github.com/ibm-cloud-security/appid-clientsdk-swift" target="_blank">Swift <img src="../../icons/launch-glyph.svg" alt="External link icon"></a> mobile clients.

## Accessing user attributes with the Android SDK
{: #accessing}

When you obtain an access token, it is possible to gain access to the user protected attributes endpoint. You can gain access by using the following API methods.

  ```java
  void setAttribute(@NonNull String name, @NonNull String value, UserAttributeResponseListener listener);
  void setAttribute(@NonNull String name, @NonNull String value, @NonNull AccessToken accessToken, UserAttributeResponseListener listener);

  void getAttribute(@NonNull String name, UserAttributeResponseListener listener);
  void getAttribute(@NonNull String name, @NonNull AccessToken accessToken, UserAttributeResponseListener listener);

  void deleteAttribute(@NonNull String name, UserAttributeResponseListener listener);
  void deleteAttribute(@NonNull String name, @NonNull AccessToken accessToken, UserAttributeResponseListener listener);

  void getAllAttributes(@NonNull UserAttributeResponseListener listener);
  void getAllAttributes(@NonNull AccessToken accessToken, @NonNull UserAttributeResponseListener listener);
  ```
  {: pre}

When an access token is not explicitly passed, {{site.data.keyword.appid_short_notm}} uses the last received token.

For example, you can call the following code to set a new attribute, or override an existing one.

  ```java
  appId.getUserAttributeManager().setAttribute(name, value, useThisToken,new UserAttributeResponseListener() {
		@Override
		public void onSuccess(JSONObject attributes) {
			//attributes received in JSON format on successful response
		}

		@Override
		public void onFailure(UserAttributesException e) {
			//Exception occurred
		}
	});
  ```
  {: pre}

### Anonymous login
{: #anonymous notoc}

With {{site.data.keyword.appid_short_notm}}, you can log in [anonymously](/docs/services/appid/user-profile.html#anonymous).

  ```java
  appId.loginAnonymously(getApplicationContext(), new AuthorizationListener() {
		@Override
		public void onAuthorizationFailure(AuthorizationException exception) {
			//Exception occurred
		}

		@Override
		public void onAuthorizationCanceled() {
			//Authentication canceled by the user
		}

		@Override
		public void onAuthorizationSuccess(AccessToken accessToken, IdentityToken identityToken, RefreshToken refreshToken) {
			//User authenticated
		}
	});
  ```
  {: pre}

### Progressive authentication
{: #progressive notoc}

When the user holds an anonymous access token, they can become identified by passing it to the `loginWidget.launch` method.

  ```java
  void launch (@NonNull final Activity activity, @NonNull final AuthorizationListener authorizationListener, String accessTokenString);
  ```
  {: pre}

After an anonymous login, progressive authentication occurs even if the login widget is called without passing an access token because the service used the last received token. If you want to clear your stored tokens, run the following command.

  ```java
  	appIDAuthorizationManager = new
    AppIDAuthorizationManager(this.appId);
    appIDAuthorizationManager.clearAuthorizationData();
  ```
  {: pre}


## Accessing user attributes with the iOS SDK
{: #accessing}

Access to the users attributes by passing an access token through the following API methods.
{: shortdesc}

  ```swift
  func setAttribute(key: String, value: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)
  func setAttribute(key: String, value: String, accessTokenString: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)

  func getAttribute(key: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)
  func getAttribute(key: String, accessTokenString: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)

  func getAttributes(completionHandler: @escaping(Error?, [String:Any]?) -> Void)
  func getAttributes(accessTokenString: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)

  func deleteAttribute(key: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)
  func deleteAttribute(key: String, accessTokenString: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)
  ```
  {: pre}

When an access token is not explicitly passed, {{site.data.keyword.appid_short_notm}} uses the last received token.

For example, you can call the following code to set a new attribute, or override an existing one.

  ```swift
  AppID.sharedInstance.userAttributeManager?.setAttribute("key", "value", completionHandler: { (error, result) in
      if error = nil {
          //Attributes recieved as a Dictionary
      } else {
          // An error has occurred
      }
  })
  ```
  {: pre}


### Anonymous login

With {{site.data.keyword.appid_short_notm}}, you can log in [anonymously](/docs/services/appid/user-profile.html#anonymous).

  ```swift
  class delegate : AuthorizationDelegate {

      public func onAuthorizationSuccess(accessToken: AccessToken, identityToken: IdentityToken, refreshToken: RefreshToken?, response:Response?) {
          //User authenticated
      }

      public func onAuthorizationCanceled() {
          //Authentication canceled by the user
      }

      public func onAuthorizationFailure(error: AuthorizationError) {
          //Error occurred
      }
   }

  AppID.sharedInstance.loginAnonymously( authorizationDelegate: delegate())
  ```
  {: pre}

### Progressive authentication

When you hold an anonymous access token, the user can become an identified user by passing it to the `loginWidget.launch` method.

  ```swift
  func launch(accessTokenString: String? , delegate: AuthorizationDelegate)
  ```
  {: pre}

After an anonymous login, progressive authentication occurs even if the login widget is called without passing an access token because the service used the last received token. If you want to clear your stored tokens, run the following command.

  ```swift
  var appIDAuthorizationManager = AppIDAuthorizationManager(appid: AppID.sharedInstance)
  appIDAuthorizationManager.clearAuthorizationData()
  ```
  {: pre}

## Data separation and encryption
{: #data}

{{site.data.keyword.appid_short_notm}} stores and encrypts user profile attributes. As a multi-tenant service, every tenant has a designated encryption key and user data in each tenant is encrypted with only that tenant's key.

{{site.data.keyword.appid_short_notm}} ensures that private information is encrypted before it is stored.
