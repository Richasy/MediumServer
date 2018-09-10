# Medium SDK for C#

This repository contains the open source SDK for integrating [Medium's OAuth2 API]("https://github.com/Medium/medium-api-docs") into your universal app.

Upgrading and transformation based on [medium-sdk-dotnet](https://github.com/adriangodong/medium-sdk-dotnet). Thanks it.

---

### Usage

#### Build client

Medium has two ways of verification. `OAuth` and `Integration tokens`. Anyway, you have to [register](https://medium.com/me/applications) an application first.

When you register a **Medium Application**, you get the `Client ID` and the `Client Secret`. They can help you build the Medium client. like this:

```csharp
// Build it to use the function
Client _client = new Client(clientId, clientSecret);
```

After you build the client, you need to set up Token, so how do you get it?

###### OAuth

1. Get authorization link
    ```csharp
    // The [secretstate] is your special sign
    // The [callbackUri] is the callback URl that you filled in when you registered app.
    // The [scope] is your permission scope. Note. {uploadImage}, please use it prudently.
    string url = _client.GetAuthorizeUrl("secretstate", callbackUri, scope);
    ```
2. Open browser to access this link
    ```csharp
    // Use this in your app.
    await Launcher.LaunchUriAsync(new Uri(url));
    ```
3. Let the user enter the code value displayed in the callback link.
    ```csharp
   Token token = await _client.GetAccessToken(code, callbackUri);
    ```
    Now that you have the token, save it properly (locally or otherwise), the token is valid for two months, and when it expires, it can be retrieved with the `RefreshToken`.
    ```csharp
    Token newToken = await _client.GetAccessToken(oldToken.RefreshToken);
    ```
###### Integration token

This is a very simple way to authorize, User generate an Integration token in settings, and you only need to provide an input method for the user to get the token.

When you get the token, you can generate a Token instance like:

```csharp
Token token = new Token(integationToken);
```

Either way, when you get a token, you need to import the token into the client.

```csharp
_client.SetToken(token);
```

Now, you can use the relevant methods provided by the client.

#### Methods

- **GetUser()**:

    ```csharp
    // Please get the user when build the client. you need the userId to use other methods.
   User user = await _client.GetUser();
    ```
- **GetAllPublications(string userId)**:

    ```csharp
    List<Publication> publications = (await _client.GetAllPublications(user.Id)).ToList();
    ```
- **GetAllContributors(string userId)**:

    ```csharp
    List<Contributor> contributors = (await _client.GetAllContributors(user.Id)).ToList();
    ```
- **CreatePostRequest(string userId, string title,string content, string[] tags, ContentFormat format)**

    ```csharp
    // Quick create a post request, the PublishStatus default value is Public
    RequestPostModel requestPost = _client.CreatePostRequest(userId, title, content, tags, format);
    ```
- **CreatePost(string userId, RequestPostModel requestPost)**

    ```csharp
    // Without Publication
    Post post = await _client.CreatePost(userId, requestPost);
    ```
- **CreatePostUnderPublication(string publicationId, RequestPostModel requestPost)**

    ```csharp
    // With Publication
    Post post = await _client.CreatePostUnderPublication(publicationId, requestPost);
    ```
- **CreateImageRequest(StorageFile image)**

    ```csharp
    // Build request from StorageFile, but you must have authority[uploadImage]
    RequestImageModel requestImage = await _client.CreateImageRequest(image);
    ```
- **UploadImage(RequestImageModel requestImage, string name)**

    ```csharp
    Image image = await _client.UploadImage(requestImage, imageName);
    ```
    
#### Event

- **OnError\<Exception\>**
    
    This event is to deal with errors in the operation of the program.
    
- **OnRequestFailed\<MediumErrorResponse\>**
    
    This event is to deal with the error message returned by the website when launching the network request.

---

### Example

In order to better demonstrate how to use this sdk, I made a universal application. Please check [here](https://github.com/Richasy/MediumServerExample).

---

### Contributing

Questions, comments, bug reports, and pull requests are all welcomed.

---

### Authors

[Richasy](https://github.com/Richasy)

---

### License

Licensed under The [MIT License](https://github.com/adriangodong/medium-sdk-dotnet/blob/master/LICENSE).






