Authentication
===================================

Our APIs use `Basic access authentication <https://en.wikipedia.org/wiki/Basic_access_authentication>`_ for every API endpoint.

If you do not have a login and a password, ask your Homeloc contact for one. We will create one for you to access all our APIs.

Our APIs are all available in HTTPS to protect your credentials so make sure to call the secured endpoints :)

Here are a few examples to see how to handle the authentication:

* Access the url using your browser. You will be prompted for a login and a password.

* Use curl::

    curl --user $user:$password $api_endpoint_url

* Use Python and requests::

    import requests

    USERNAME = 'james_watson'
    PASSWORD = 'ilovesherlock'
    url = 'https://homelocpay.net/api_endpoint/'
    auth = (USERNAME, PASSWORD)
    print requests.get(url, auth=auth)
