Setting up Social Authentication via OAuth2 Providers
=====================================================


Auth0 OAuth2 Configuration
--------------------------

In the same way as with other Identiy-Providers, it's now possible to leverage Auth0 to authenticate users on DefectDojo.

1. Inside your Auth0 dashboard create a new application (Applications / Create Application / Single Page Web Application).

2. On the new application set the following fields:

   * Name: "Defectdojo"
   * Allowed Callback URLs: "https://the_hostname_you_have_dojo_deployed:your_server_port/complete/auth0/"

3. Copy the following info from the application:

   * Domain
   * Client ID
   * Client Secret

3. Now, edit the dojo/settings.py file and edit/replace the following information:

  * DD_SOCIAL_AUTH_AUTH0_OAUTH2_ENABLED=True
  * DD_SOCIAL_AUTH_AUTH0_KEY=(str, '**YOUR_CLIENT_ID_FROM_STEP_ABOVE**'),
  * DD_SOCIAL_AUTH_AUTH0_SECRET=(str, '**YOUR_CLIENT_SECRET_FROM_STEP_ABOVE**'),
  * DD_SOCIAL_AUTH_AUTH0_DOMAIN=(str, '**YOUR_AUTH0_DOMAIN_FROM_STEP_ABOVE**'),

5. Restart DefectDojo, and you should now see a **Login with Auth0** button on the login page.


Google
------

New to DefectDojo, a Google account can now be used for Authentication, Authorization, and a DefectDojo user. Upon login with a Google account, a new user will be created if one does not already exist. The criteria for determining whether a user exists is based on the users username. In the event a new user is created, the username is that of the Google address without the domain. Once created, the user creation process will not happen again as the user is recalled by its username, and logged in. In order to make the magic happen, a Google authentication server needs to be created. Closely follow the steps below to guarantee success.

1. Navigate to the following address and either create a new account, or login with an existing one: `Google Developers Console`_ 
2. Once logged in, find the key shaped button labeled **Credentials** on the left side of the screen. Click **Create Credentials**, and choose **OAuth Client ID**:

.. image:: /_static/google_1.png

3. Select **Web Applications**, and provide a descriptive name for the client.

.. image:: /_static/google_2.png

4. Add the pictured URLs in the **Authorized Redirect URLs** section. This part is very important. If there are any mistakes here, the authentication client will not authorize the request, and deny access.
5. Once all URLs are added, finish by clicking **Create**

Now with the authentication client created, the **Client ID** and **Client Secret Key** need to be copied over to settings.py in the project. Click the newly created client and copy the values:

.. image:: /_static/google_3.png

In the **Environment** section at the top of settings.py, enter the values as shown below:

.. image:: /_static/google_4.png

In the **Authentication** section of settings.py, set **DD_GOOGLE_OAUTH_ENABLED** to **True** to redirect away from this README and actually authorize.

.. image:: /_static/google_5.png

To authorize users you will need to set the following:

  * SOCIAL_AUTH_GOOGLE_OAUTH2_WHITELISTED_DOMAINS = ['example.com', 'example.org']

or

  * SOCIAL_AUTH_GOOGLE_OAUTH2_WHITELISTED_EMAILS = ['email@example.com']

.. _Google Developers Console: https://console.developers.google.com


OKTA
----

In a similar fashion to that of Google, using OKTA as a OAuth2 provider carries the same attributes and a similar procedure. Follow along below.

1. Navigate to the following address and either create a new account, or login with an existing one: `OKTA Account Creation`_
2. Once logged in, enter the **Applications** and click **Add Application**:

.. image:: /_static/okta_1.png

3. Select **Web Applications**.

.. image:: /_static/okta_2.png

4. Add the pictured URLs in the **Login Redirect URLs** section. This part is very important. If there are any mistakes here, the authentication client will not authorize the request, and deny access. Check the **Implicit** box as well.

.. image:: /_static/okta_3.png

5. Once all URLs are added, finish by clicking **Done**.
6. Return to the **Dashboard** to find the **Org-URL**. Note this value as it will be important in the settings file. 

.. image:: /_static/okta_4.png

Now, with the authentication client created, the **Client ID** and **Client Secret** Key need to be copied over to settings.py in the project. Click the newly created client and copy the values:

.. image:: /_static/okta_5.png

In the **Environment** section at the top of settings.py, enter the values as shown below:

.. image:: /_static/okta_6.png

In the **Authentication** section of settings.py, set **DD_OKTA_OAUTH_ENABLED** to **True** to redirect away from this README and actually authorize.

.. image:: /_static/okta_7.png

.. _OKTA Account Creation: https://www.okta.com/developer/signup/


If during the login process you get the following error: *The 'redirect_uri' parameter must be an absolute URI that is whitelisted in the client app settings.* and the `redirect_uri` HTTP GET parameter starts with `http://` instead of `https://` you need to add **SOCIAL_AUTH_REDIRECT_IS_HTTPS = True** in the **Authentication** section of settings.py.



Azure Active Directory Tenant Configuration
-------------------------------------------
You can now use your corporate Azure Active Directory to authenticate users to Defect Dojo.
Users will be using your corporate Azure AD account (A.K.A. Office 365 identity) to authenticate via OAuth, and all the conditional access rules and benefits from Azure Active Directory will also apply to the Defect Dojo Authentication.
Once the user signs in, it will try to match the UPN of the user to an existing e-mail from a user in Defect Dojo, and if no match is found, a new user will be created in Defect Dojo, associated with the unique id/value of the user provided by your Azure AD tenant. Then, you can assign roles to this user, such as ‘staff‘ or ‘superuser‘

1. Navigate to the following address and follow instructions to create a new app registration

  * https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app

2. Once you register an app, take note of the following information:

  * **Application (client) ID**
  * **Directory (tenant) ID** 
  * Under Certificates & Secrets, create a new **Client Secret** 
  
3. Under Authentication > Redirect URIs, add a *WEB* type of uri where the redirect points to

  * http://localhost:8080/complete/azuread-tenant-oauth2/  
  * **OR**   
  * https://the_hostname_you_have_dojo_deployed:your_server_port/complete/azuread-tenant-oauth2/

4. Now, edit the dojo/settings.py file and edit/replace the following information:

  * DD_SOCIAL_AUTH_AZUREAD_TENANT_OAUTH2_KEY=(str, '**YOUR_APPLICATION_ID_FROM_STEP_ABOVE**'),
  * DD_SOCIAL_AUTH_AZUREAD_TENANT_OAUTH2_SECRET=(str, '**YOUR_CLIENT_SECRET_FROM_STEP_ABOVE**''),
  * DD_SOCIAL_AUTH_AZUREAD_TENANT_OAUTH2_TENANT_ID=(str, '**YOUR_DIRECTORY_ID_FROM_STEP_ABOVE**''),
  * AZUREAD_TENANT_OAUTH2_ENABLED = **True** 
  
5. Restart your Dojo, and you should now see a **Login with Azure AD** button on the login page which should *magically* work


Gitlab OAuth2 Configuration
---------------------------
In a similar fashion to that of Google and OKTA, using Gitlab as a OAuth2 provider carries the same attributes and a similar procedure. Follow along below.

1. Navigate to your Gitlab settings page and got to the Applications section

  * https://gitlab.com/profile/applications
  * **OR**
  * https://the_hostname_you_have_gitlab_deployed:your_gitlab_port/profile/applications

2. Choose a name for your application

3. For the Redirect URI, enter the DefectDojo URL with the following format

  * https://the_hostname_you_have_dojo_deployed:your_server_port/complete/gitlab/

4. Now, edit the dojo/settings.py file and edit/replace the following information:

  * DD_SOCIAL_AUTH_GITLAB_KEY=(str, '**YOUR_APPLICATION_ID_FROM_STEP_ABOVE**'),
  * DD_SOCIAL_AUTH_GITLAB_SECRET=(str, '**YOUR_SECRET_FROM_STEP_ABOVE**'),
  * DD_SOCIAL_AUTH_GITLAB_API_URL=(str, '**https://gitlab.com**'),
  * DD_SOCIAL_AUTH_GITLAB_OAUTH2_ENABLED = **True**

  Additionally, if you want to import your Gitlab projects as DefectDojo products, add the following line, still in dojo/settings.py:
  
  * DD_SOCIAL_AUTH_GITLAB_PROJECT_AUTO_IMPORT = **True**

5. Restart DefectDojo, and you should now see a **Login with Gitlab** button on the login page.


User Permissions
----------------

When a new user is created via the social-auth, the default permissions are only active. This means that the newly created user does not have access to add, edit, nor delete anything within DefectDojo. To circumvent that, a custom pipeline was added (dojo/pipline.py/modify_permissions) to elevate new users to staff. This can be disabled by setting ‘is_staff’ equal to False. Similarly, for an admin account, simply add the following to the modify_permissions pipeline:
	is_superuser  = True

Exception for Gitlab OAuth2: with DD_SOCIAL_AUTH_GITLAB_PROJECT_AUTO_IMPORT set to True in dojo/settings.py, where a new user is created via the Gitlab social-auth, he has one permission: add_engagement. It allows him to create further engagements on his products via API v1.

Other Providers
---------------

In an effort to accommodate as much generality as possible, it was decided to implement OAuth2 with the `social-auth`_ ecosystem as it has a library of compatible providers with documentation of implementation. Conveniently, each provider has an identical procedure of managing the authenticated responses and authorizing access within a given application. The only difficulty is creating a new authentication client with a given OAuth2 provider. 

.. _social-auth: https://github.com/python-social-auth/social-core/tree/master/social_core/backends

