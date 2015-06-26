How to handle API responses
===============================

===============
Response Format
===============
Our API can serve XML or JSON. Given a API endpoint, just add the format you want as a query string.

* XML format: Add ``?format=xml`` at the end of the API endpoint url

* JSON format: Add ``?format=json`` at the end of the API endpoint url

==========
Pagination
==========
Expect results to be paginated. If you want to fetch all the data one API endpoint can provide, you will have to go through all the pages yourself.

In JSON, responses from our API look like this::

    {
        'count': 142,
        'next': 'http://homelocpay.net/api_endpoint/?page=2&format=json'
        'previous': null,
        'results': [
            {
                'id': 1,
                'data': 'example',
            },
            {
                'id': 2,
                'data': 'example2',
            }
        ]
    }


* ``count`` contains the number of items in total

* If the current page is not the last one, ``next`` will contain the url of the next page

* If the current page is not the first one, ``previous`` will contain the url of the previous page

Here is a code example in Python where we go through every page to fetch all the data contained in an api endpoint::

    import requests

    USERNAME = 'james_watson'
    PASSWORD = 'ilovesherlock'
    url = 'https://homelocpay.net/api_endpoint/'
    auth = (USERNAME, PASSWORD)

    all_results = []
    while True:
        r = requests.get(auth=auth, url)
        r = r.json()
        results = r.get('results', [])
        next = r.get('next')
        all_results += results
        if not next:
            break
        url = next

    print all_results
