# darkseid

Configuration for access token to send upn from azure ad
To configure an Azure AD app registration so that the user UPN (User Principal Name) is included in the access token, follow these steps:

### Step 1: Register the Application

1. **Go to Azure Portal**: Navigate to the Azure Active Directory section.
2. **App registrations**: Click on "App registrations" in the left menu.
3. **New registration**: Click on "New registration."
4. **Fill in the details**: Provide a name, select supported account types, and specify the redirect URI if needed.
5. **Register**: Click "Register."

### Step 2: Configure Token Claims

1. **Expose an API**: In your app registration, go to "Expose an API."
2. **Add a scope**: Click on "Add a scope." You can create a scope that will define what claims are included in the token.
3. **Set up claims**: In the "Claims" section, ensure the UPN claim (`upn`) is included. If it’s not there, you might need to create a custom claim.

### Step 3: Modify Manifest (if necessary)

1. **Manifest**: Click on "Manifest" in the left menu.
2. **Add claims**: Look for the `"optionalClaims"` section and ensure that the UPN claim is added under `"idToken"` or `"accessToken"`.

   Here’s an example of what you might add:
   ```json
   "optionalClaims": {
       "idToken": [],
       "accessToken": [
           {
               "name": "upn",
               "essential": false
           }
       ]
   }
   ```

### Step 4: API Permissions

1. **API permissions**: Click on "API permissions."
2. **Add permissions**: Ensure the app has the necessary permissions to access user details. Typically, you would require `User.Read` under Microsoft Graph.

### Step 5: Grant Admin Consent

1. **Admin consent**: If required, make sure to grant admin consent for the permissions you've added.

### Step 6: Test the Configuration

1. **Obtain an access token**: Use a tool like Postman or a custom application to request an access token.
2. **Inspect the token**: Decode the JWT to ensure the UPN is included in the claims.

### Conclusion

Following these steps will allow you to configure your Azure AD app registration to include the UPN value in the access token. If you run into any issues, check the Azure documentation for updates or specific configurations related to your application type.
Elaborating step 2
### Step 2: Configure Token Claims and Scopes

#### Why Add a Scope?

In Azure AD, scopes define the permissions that the application can request from the user. Scopes are crucial because they help manage what data can be accessed and ensure that tokens issued to applications only contain the necessary information. Here’s a deeper look:

1. **Security Control**: Scopes allow you to limit the access that applications have. By explicitly defining scopes, you ensure that an application only gets the permissions it explicitly requests and the user consents to.

2. **User Consent**: When an application requests access to resources, users are shown the scopes being requested. This transparency allows users to understand what data they are sharing.

3. **Token Claims Management**: When you create a scope, you can also specify which claims should be included in the access token. This means you can directly control whether the UPN (or any other claims) is present in the token.

#### Are We Using Scopes in This Case?

In the context of including the UPN in the access token, here’s how scopes come into play:

- **Defining a Scope**: While you technically don't need to create a new scope solely for the UPN, defining one can help ensure that the access token is tailored for your application’s specific needs. For instance, if you want the access token to only include specific claims (like UPN), you would define a scope to enforce that.

- **Claim Inclusion**: By creating a scope, you can explicitly define which claims (such as `upn`) are included in the token for that specific scope. This way, when the application requests a token with that scope, it can receive the UPN claim in the response.

### Practical Steps to Add a Scope

1. **Navigate to "Expose an API"**: In your app registration, go to the "Expose an API" section.
2. **Add a Scope**: Click on "Add a scope."
   - **Scope Name**: Choose a name for your scope (e.g., `access_as_user`).
   - **Who can consent?**: Select whether users or admins can consent.
   - **Add a description**: Provide a brief description of what this scope allows.
   - **Claim Configuration**: Here, you can define what claims should be included when this scope is requested.

3. **Request the Scope in Your Application**: When your application requests an access token, it should include the scope you defined. This is done by specifying it in the OAuth 2.0 token request.

### Conclusion

While you can technically include claims like UPN without creating a new scope, using scopes provides better control, security, and clarity about what data is being accessed. This approach is particularly useful as your application grows and requires more granular permissions.


configuration for postmant

To use the defined scope in Postman to obtain an access token that includes the UPN claim, follow these steps:

### Step 1: Set Up Postman

1. **Open Postman**: Launch the Postman application.

### Step 2: Create a New Request

1. **Create a new request**: Click on “New” and select “Request.”
2. **Name your request**: Give it a meaningful name and save it in a collection.

### Step 3: Configure OAuth 2.0 Authorization

1. **Authorization Tab**: Select the “Authorization” tab in your request.
2. **Type**: Choose "OAuth 2.0" from the dropdown.

### Step 4: Configure the OAuth 2.0 Settings

Fill in the necessary fields as follows:

- **Token Name**: Give a name to this token (e.g., `MyAccessToken`).
- **Grant Type**: Select “Authorization Code” or “Client Credentials” depending on your flow. For user authentication, use "Authorization Code."
- **Callback URL**: This should match the redirect URI configured in your Azure AD app registration.
- **Auth URL**: This should be in the following format:
  ```
  https://login.microsoftonline.com/{tenant-id}/oauth2/v2.0/authorize
  ```
  Replace `{tenant-id}` with your Azure AD tenant ID.

- **Access Token URL**: Use:
  ```
  https://login.microsoftonline.com/{tenant-id}/oauth2/v2.0/token
  ```

- **Client ID**: Your application’s Client ID from Azure AD.
- **Client Secret**: Your application’s Client Secret (if applicable).
- **Scope**: Enter the scope you defined, along with the API identifier. The format should look like this:
  ```
  api://{client-id}/{scope-name}
  ```
  For example:
  ```
  api://12345678-1234-1234-1234-123456789abc/access_as_user
  ```

- **Client Authentication**: Set this to “Send client credentials in body” if applicable.

### Step 5: Request Token

1. **Get New Access Token**: Click the “Get New Access Token” button.
2. **Log In**: A login screen should pop up. Log in with a user account that has permission to access the scope.
3. **Authorize**: After logging in, you will need to consent to the permissions requested.

### Step 6: Use the Access Token

1. **Token Received**: Once you have received the token, click “Use Token.” This will automatically set the `Authorization` header in your request.
2. **Send the Request**: Make the API call that requires this access token.

### Step 7: Decode the Token

1. **Decode JWT**: Use a JWT decoder (like [jwt.io](https://jwt.io/)) to inspect the token and verify that it includes the UPN claim.

### Conclusion

By following these steps, you will be able to configure Postman to request an access token that includes the UPN claim using the defined scope in your Azure AD app registration. If you encounter any issues, check the configuration details in both Postman and Azure AD to ensure everything is set up correctly.