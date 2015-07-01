Real time price check
=====================

This endpoint lets you know the price of a stay for particular dates and birth dates of guests (not mandatory, see below).

Do note that this endpoint does not check the availability of the rental, it only returns a price for the stay. Make sure the rental is available before calling this endpoint.

This endpoint does not need authentication (we use it internally for AJAX calls).

URL
###

This api endpoint is accessible at ``/{lang}/bookingengine/check_price/`` (replace ``{lang}`` with ``fr`` or ``en``) via POST, using the ``sub_instance_url`` (see the Rental api endpoint)


Expected fields
###############

* ``start``: The start date of the stay. Format: ``DD/MM/YYYY``

* ``end``: The end date of the stay. Format: ``DD/MM/YYYY``

* ``rental``: The ID of the rental (see the Rental api endpoint)

* ``birth_dates``: A list of the guests' birth dates. Birth dates should have the format ``YYYY-MM-DD`` and be separated by commas.


.. _birth_dates_detail:

Details for birth dates
***********************

We use birth dates for detailed pricing since prices can vary based on the guest's age during the stay (discount for children, babies etc.). The ``birth_dates`` field is mandatory but each birth date will default to ``1970-01-01``.

For example, if you want to get a price for 3 adults, you can use: ``birth_dates = ',,'``, which is equivalent to ``birth_dates = '1970-01-01,1970-01-01,1970-01-01'``.


Response
########

The response is given in JSON form.


Valid response
**************

Here is an example of a valid response::

    {
        'status': 'ok',
        'price': '158.65'
    }

The ``status`` key indicates the call you made was valid and we were able to calculate a price.

The ``price`` contains a string indicating the rent price for this stay. The price the client will pay in the end might be different if she adds options to her stay but this rent price is definitive.


Invalid response
****************
 ::

    {
        'status': 'error',
        'error': 'This rental is limited to 3 people'
    }

The ``status`` is not ``ok``, the call you made was not valid (or we were not able to calculate a price).

The ``error`` key contains a string indicating what went wrong. This string can be shown to the client, and it should help you understand what went wrong.


Possible errors
---------------

This list is not exhaustive and error strings are prone to changes. It should give you an idea of what to expect though.

* ``This rental is limited to {n} people``: The rental you asked a price for cannot hold that many people.

* ``It cannot be booked less than {n} days``: The rental you asked a price for cannot be booked for such a short period.

* ``It cannot be booked more than {n} days``: The rental you asked a price for cannot be booked for such a long period.

* ``start``, ``end``, ``rental``, ``birth_dates``: You made an error in the mentionned field (or it is missing).


Examples
########


Valid call
**********

One night stay for two adults

curl
----
 ::

    curl 'https://example.homelocpay.net/en/bookingengine/check_price/' --data 'start=17/09/2015&end=18/09/2015&rental=6&birth_dates=,'

Python
------
 ::

    import requests

    response = requests.post(
        'https://example.homelocpay.net/en/bookingengine/check_price/',
        data={
            'start': '17/09/2015',
            'end': '18/09/2015',
            'rental': '6',
            'birth_dates': ',',
        }
    )

API response
------------
 ::

    {
        'status': 'ok',
        'price': '155.55'
    }


Missing date in POST data
*************************

curl
----
 ::

    curl 'https://example.homelocpay.net/en/bookingengine/check_price/' --data 'end=18/09/2015&rental=6&birth_dates=,'

API response
------------
 ::

    {
        'status': 'error',
        'error': 'start'
    }


Invalid date format in POST data
********************************

curl
----
 ::

    curl 'https://example.homelocpay.net/en/bookingengine/check_price/' --data 'start=17-09-2015&end=18-09-2015&rental=6&birth_dates=,'

API response
------------
 ::

    {
        'status': 'error',
        'error': 'start / end'
    }


Invalid dates in POST data
**************************

``start`` is after ``end``

curl
----
 ::

    curl 'https://example.homelocpay.net/en/bookingengine/check_price/' --data 'start=19/09/2015&end=18/09/2015&rental=6&birth_dates=,'

API response
------------
 ::

    {
        'status': 'error',
        'error': 'It cannot be booked less than 1 days'
    }
