Booking creation
================

This endpoint lets you push a booking in our system. Booking you push to our system are definitive, you should not push them before making sure that the client successfully paid (on your side).

This endpoint shares a lot of similarities with the :doc:`availability_check` and :doc:`price_check` endpoints, both in the fields we expect from you and the errors you can receive.

You can cancel the booking you create here using the :doc:`cancellation`.


URL
###

This api endpoint is accessible via ``/{lang}/bookingengine/create_booking/`` (replace ``{lang}`` with ``fr`` or ``en``) via POST, using the ``sub_instance_url`` (see the Rental api endpoint)


Expected fields
###############


Required fields
***************

* ``start``: The start date of the stay. Format: ``DD/MM/YYYY``

* ``end``: The end date of the stay. Format: ``DD/MM/YYYY``

* ``rental``: The ID of the rental (see the Rental api endpoint)

* ``origin``: The name of your configured origin. Ask your Homeloc contact if you don't have one already.

* ``tenant_firstname``: The first name of the tenant

* ``tenant_lastname``: The last name of the tenant

* ``tenant_email``: The email of the tenant

* ``tenant_birthdate``: The birth date of the tenant. Format: ``YYYY-MM-DD``


Optional fields
***************

* ``guests_firstname``: A list of the guests' first names. Names should be separated with commas.

* ``guests_lastname``: A list of the guests' last names. Names should be separated with commas.

* ``guests_birthdate``: A list of the guests' birth dates. Birth dates should have the format ``YYYY-MM-DD`` and be separated by commas.


Details for guests data
-----------------------

The ``guests_birthdates`` field behaves the same way as in the :ref:`price check <birth_dates_detail>`.

First and last names are not mandatory for guests and will default to ``.``. Only the birth dates field will be used to calculate how many guests are present in the booking.

Do note that the tenant must not be contained in the guests data, otherwise she will be counted twice.


Response
########

The response is given in JSON form.


Valid response
**************

Here is an example of a valid response::

    {
        'status': 'ok',
        'reservation_id': 5432,
        'price': '158.65',
    }

The ``status`` key indicates the call you made was valid and we were able to create a booking.

The ``reservation_id`` key is the internal id of the booking you just created. You will need it later on if you want to cancel the booking. Keep it close by :)

The ``price`` key contains a string indicating the price of the stay. If you did not subscribe to any option, this price should be the same as the one you get with the :doc:`price_check`.


Invalid response
****************
 ::

    {
        'status': 'error',
        'code': 'too_many_guests'
        'error': 'This rental is limited to 3 people'
    }

The ``status`` is not ``ok``, you call you made was not valid (or we were not able to create a booking).

The ``code`` contains a string telling you what the problem is. You should use this to handle the API's response. See below for the possible values.

``error`` contains a string that you can show to the client to explain why the call did not lead to a booking.


Possible error codes
--------------------

Some of these codes are the same than those of the :ref:`availability check<availability_check_error_codes>`.

* ``bad_dates``: The dates you provided are not valid (is ``start`` before ``end``?)

* ``unavailable``: The rental is not available for the dates you specified

* ``not_bookable_online``: This code means that you cannot book the rental online. It usually means we don't have control over the stock for this rental

* ``min_duration``: The stay you specified does not respect the minimum booking duration (see the Rental API)

* ``max_duration``: The stay you specified does not respect the maximum booking duration (see the Rental API)

* ``too close``: The stay you specified is too close. Some rentals need to be booked in advance (usually a couple of days).

* ``too far``: The stay you specified is too far. Some rentals cannot be booked too long in advance (usually more than a year).

* ``badly_filled_form``: This means some (at least one) of the fields were not properly filled. In this case, the ``error`` of the response contains a list of the badly filled fields.

* ``origin_unknown``: The origin you gave us does not match any origin on our side.

* ``tenant_too_young``: The tenant is not 18 (she needs to be at least 18 on the day of the booking).

* ``too_many_guests``: The rental you chose cannot hold that many guests.


Examples
########


Valid call that creates a booking
*********************************

Booking for two adults

Python
------
 ::

    import requests

    response = requests.post(
        'https://example.homelocpay.net/en/bookingengine/create_booking',
        data={
            'start': '17/09/2015',
            'end': '18/09/2015',
            'rental': '6',
            'origin': 'My Partner Name',
            'tenant_firstname': 'John',
            'tenant_lastname': 'Watson',
            'tenant_email': 'john@watson.com',
            'tenant_birthdate': '1980-01-01',
            'guests_firstname': 'Sherlock',
            'guests_lastname': 'Holmes',
            'guests_birthdates': '1980-01-01',
        }
    )

API response
------------
 ::

    {
        'status': 'ok',
        'reservation_id': 221,
        'price': '155.55'
    }


Too many guests
***************

Booking for four adults when the rental has a maximal capacity of 3

Python
------
 ::

    import requests

    response = requests.post(
        'https://example.homelocpay.net/en/bookingengine/create_booking',
        data={
            'start': '17/09/2015',
            'end': '18/09/2015',
            'rental': '6',
            'origin': 'My Partner Name',
            'tenant_firstname': 'John',
            'tenant_lastname': 'Watson',
            'tenant_email': 'john@watson.com',
            'tenant_birthdate': '1980-01-01',
            'guests_firstname': '',
            'guests_lastname': '',
            'guests_birthdates': ',,',
        }
    )

API response
------------
 ::

    {
        'status': 'error',
        'code': 'too_many_guests'
        'error': 'This rental is limited to 3 people'
    }
