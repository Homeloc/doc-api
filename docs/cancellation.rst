Booking cancellation
====================

This endpoint lets you cancel a booking you created.


URL
###

This api endpoint is accessible via ``/{lang}/bookingengine/cancel_booking/`` (replace ``{lang}`` with ``fr`` or ``en``) via POST, using the ``sub_instance_url`` (see the Rental api endpoint)


Expected fields
###############

* ``origin``: The name of your configured origin. Ask your Homeloc contact if you don't have one already.

* ``reservation``: The id of the booking you created. You received it when you created the booking.


Response
########

The response is given in JSON form.


Valid response
**************

Here is an example of a valid response::

    {
        'status': 'ok',
        'paid_amount': '24.95'
    }

The ``status`` key indicates the call you made was valid and we were able to cancel the booking.

The ``paid_amount`` shows how much was already paid for the booking (on our platform). If you handle the payments on your own you can safely ignore it.


Invalid response
****************
 ::

    {
        'status': 'error',
        'code': 'already_cancelled',
        'error': 'The booking is already cancelled'
    }

The ``status`` is not ``ok``, you call you made was not valid (or we were not able to cancel the booking).

The ``code`` contains a string telling you what the problem is. You should use this to handle the API's response. See below for the possible values.

``error`` contains a string explaining what happened in more details.


Possible error codes
--------------------

* ``badly_filled_form``: This means some (at least one) of the fields were not properly filled. In this case, the ``error`` of the response contains a list of the badly filled fields.

* ``origin_unknown``: The origin you gave us does not match any origin on our side.

* ``wrong_origin``: The origin you gave us does not match the origin of the booking you specified.

* ``already_cancelled``: The booking you are trying to cancel is already cancelled.


Examples
########


Valid call that cancels a booking
*********************************

curl
----
 ::

    curl 'https://example.homelocpay.net/en/bookingengine/cancel_booking' --data 'reservation=221&origin=My+Partner+Name'


Python
------
 ::

    import requests

    response = requests.post(
        'https://example.homelocpay.net/en/bookingengine/cancel_booking',
        data={
            'reservation': 221,
            'origin': 'My Partner Name',
        }
    )

API response
------------
 ::

    {
        'status': 'ok',
        'paid_amount': '22.10'
    }



Origin is different from booking origin
***************************************

curl
----
 ::

    curl 'https://example.homelocpay.net/en/bookingengine/cancel_booking' --data 'reservation=221&origin=Some+Other+Origin'

Python
------
 ::

    import requests

    response = requests.post(
        'https://example.homelocpay.net/en/bookingengine/cancel_booking',
        data={
            'reservation': 221,
            'origin': 'Some other origin',
        }
    )

API response
------------
 ::

    {
        'status': 'error',
        'code': 'wrong_origin',
        'error': 'You can only cancel reservations with the same origin as the one you provided'
    }
