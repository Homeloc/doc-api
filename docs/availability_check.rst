Real time availability check
============================

This endpoint lets you find out if a rental is available for particular dates. Since availabilities in the rental api endpoint are cached, you should be calling this endpoint to make sure a particular stay is available.

This endpoint does not need authentication (we use it internally for AJAX calls).


URL
###

This api endpoint is accessible at ``/{lang}/bookingengine/check_availability/`` (replace ``{lang}`` with ``fr`` or ``en``) via POST, using the ``sub_instance_url`` (see the Rental api endpoint)


Expected fields
###############

* ``start``: The start date of the stay. Format: ``DD/MM/YYYY``

* ``end``: The end date of the stay. Format: ``DD/MM/YYYY``

* ``rental``: The ID of the rental (see the Rental api endpoint)

* ``origin``: The name of your configured origin. Ask your Homeloc contact if you don't have one already.


Response
########

The response is given in JSON form.


Valid response
**************

Here is an example of a valid response::

    {
        'status': 'ok',
        'price': 150,
        'url_review': 'http://example.homelocpay.net/fr/bookingengine/{hashed_key}/review/',
    }

The ``status`` key indicates if the call you made was valid and a booking is possible.

The ``price`` key contains an **approximate** price for the stay. This price is not definitive and might change (if you add options to the stay, if some guests are below a certain age etc.). It can be used to show an estimate to the client.

The ``url_review`` contains the url of our bookingengine for this reservation. We use it internally but you might not have any use for it.


Invalid response
****************

Here is an example of an invalid response::

    {
        'status': 'error',
        'code': 'unavailable',
        'error': 'Period not available'
    }

The ``status`` is not ``ok``, the call you made was not valid or it was valid but a booking is not possible.

The ``code`` contains a string telling you what the problem is. You should use this to handle the API's response. See below for the possible values.

``error`` contains a string that you can show to the client to explain why the call did not lead to a booking.


.. _availability_check_error_codes:

Possible error codes
--------------------

* ``bad_dates``: The dates you provided are not valid (is ``start`` before ``end``?)

* ``no_rental_found``: We could not find a Rental with the ID you specified

* ``unavailable``: The rental is not available for the dates you specified

* ``not_bookable_online``: This code means that you cannot book the rental online. It usually means we don't have control over the stock for this rental

* ``min_duration``: The stay you specified does not respect the minimum booking duration (see the Rental API)

* ``max_duration``: The stay you specified does not respect the maximum booking duration (see the Rental API)

* ``too close``: The stay you specified is too close. Some rentals need to be booked in advance (usually a couple of days).

* ``too far``: The stay you specified is too far. Some rentals cannot be booked too long in advance (usually more than a year).


Examples
########


Valid call that leads to a booking
**********************************

curl
----
 ::

    curl 'https://example.homelocpay.net/en/bookingengine/check_availability/' --data 'start=17/09/2015&end=18/09/2015&rental=6&origin=My+Partner+Name'

Python
------
 ::

    import requests

    response = requests.post(
        'https://example.homelocpay.net/en/bookingengine/check_availability',
        data={
            'start': '17/09/2015',
            'end': '18/09/2015',
            'rental': '6',
            'origin': 'My Partner Name',
        }
    )

API response
------------
 ::

    {
        "status": "ok",
        "price": 101,
        "url_review": "https://example.homelocpay.net/en/bookingengine/{hashed_key}/review/",
    }


Invalid dates in POST data
**************************

curl
----
 ::

    curl 'https://example.homelocpay.net/en/bookingengine/check_availability/' --data 'start=19/09/2015&end=18/09/2015&rental=6&origin=My+Partner+Name'

API response
------------
 ::

    {
        "status": "error",
        "code": "bad_dates",
        "error": "Departure must be after arrival"
    }
