Setting up Social Authentication via OAuth2 Providers
=====================================================


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


User Permissions
----------------

When a new user is created via the social-auth, the default permissions are only active. This means that the newly created user does not have access to add, edit, nor delete anything within DefectDojo. To circumvent that, a custom pipeline was added (dojo/pipline.py/modify_permissions) to elevate new users to staff. This can be disabled by setting ‘is_staff’ equal to False. Similarly, for an admin account, simply add the following to the modify_permissions pipeline:
	is_superuser  = True


Other Providers
---------------

In an effort to accommodate as much generality as possible, it was decided to implement OAuth2 with the `social-auth`_ ecosystem as it has a library of compatible providers with documentation of implementation. Conveniently, each provider has an identical procedure of managing the authenticated responses and authorizing access within a given application. The only difficulty is creating a new authentication client with a given OAuth2 provider. 

.. _social-auth: https://github.com/python-social-auth/social-core/tree/master/social_core/backends

