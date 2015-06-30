Rental API
===================================

The Rental API lets you fetch "cold" data pertaining to the rental. It contains both editorial content (title, description, pictures, etc.) and booking data (minimum duration, availabilities etc.).

Editorial content fields
########################

These fields are not normalized, they should be used to display informations to the potential client. These infos might not be up to date.

* ``category``

* ``main_type``

* ``sub_type``

* ``floor``

* ``surface`` (usually in square meters)

* ``optimal_person_number``

* ``maximal_person_number`` (also useful for booking)

* ``nb_rooms``

* ``nb_bedrooms``

* ``nb_bathrooms``

* ``nb_separate_toilets``

* ``children_friendly``

* ``smoking_allowed``

* ``equipments``: Contains a list of string, on for each equipment

* ``exterior_equipments``: Contains a list of string, on for each exterior equipment

* ``accesses``: Contains a list of dictionary. Each dictionary has the following keys: ``type``, ``line`` and ``station``

* ``photos``: Contains a list of dictionary. Each dictionary has the following keys: ``url`` which contains the url to download the picture and ``title`` which is the title for the picture (might be empty)

* ``extra``: Contains extra info specific to this instance

* ``address``: Contains a dictionary with the following keys: ``city``, ``street``, ``zipcode``, ``district``, ``country`` and ``geo``. The values are all strings except for ``geo``: it's a dictionary with ``latitude`` and ``longitude`` as keys.

* ``environments``: Contains a list of string, one for each environment

* ``title``: Contains a dictionary where keys are languages codes (``fr``, ``en`` etc.) and values are the name of the rental for each language

* ``description``: Same as title, contains a dictionary with a description for each language

* ``price``: Contains **editorial content** about this rental's price. It can contains a dictionary with the keys ``min`` and ``type`` or just a value. Do not use this price to make booking.

Booking information fields
##########################

* ``booking_duration_min``: Minimum duration (in days) of a booking for this rental

* ``booking_duration_max``: Maximum duration (in days) of a booking for this rental

* ``maximal_person_number``: The maximum of guests this rental can hold

* ``availabilities``: Contains a list of availabilities for this rental. These periods might be out of date (by ~15 minutes), see the ``unavailability_api_feed`` field for more info.

Availabilities structure
************************

Each period is a dictionary with the following keys: ``start``, ``end`` and ``type``. Dates use this format: ``YYYY-MM-DD``.

The ``type`` of a period shows if the rental is available. If the type is ``av`` then the rental is available for the period. Otherwise it's not.

The periods should be contiguous in time: ``period[i]['end'] == period[i+1]['start']``

Example of availabilities::

    [
        {
            "start": "1970-01-01",
            "end": "2015-06-30",
            "type": "un"
        },
        {
            "start": "2015-06-30",
            "end": "2015-07-02",
            "type": "av"
        },
        {
            "start": "2015-07-02",
            "end": "2015-07-03",
            "type": "ag"
        },
        {
            ...
        },
    ]

Let's see which nights are bookable based on these periods:

* Any night up until 2015-06-29: **not bookable**
* The night between 2015-06-29 and 2015-06-30: **not bookable**
* The night between 2015-06-30 and 2015-07-01: **bookable**
* The night between 2015-07-01 and 2015-07-02: **bookable**
* The night between 2015-07-02 and 2015-07-03: **not bookable**


Other fields
############

* ``unavailability_api_feed``: Url for the freshest (un)availabilities for this rental. If empty, you are already getting the freshest availabilities. Otherwise you might be getting cached availabilities

* ``activated``: Used for internal purposes

* ``creation_date``: Creation date of the model, should not be useful to you

* ``update_date``: Update date of the model, should not be useful to you

* ``source_id``: External ID of the model, may not be unique!

* ``id``: Internal ID of the model, should not be useful for you

* ``url``: Url of the rental on this website, should be ignored
