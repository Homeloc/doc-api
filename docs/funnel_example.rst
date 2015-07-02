Full funnel example
===================

Here is a full funnel example to show how one can use our APIs. You are free to do something completely different, this is just to give you an idea.


A word on URLs
##############

Our architecture is in two parts. We have booking websites which are client facing, and back-offices for rental managers.

Booking websites syndicate some of the content from the back-offices but not everything. In particular, price data is not syndicated, it is only present in the back-offices.

Thus, you will be doing some API calls to the booking websites, and other to specific back-office website. Here is a list

Our clients each have their own URLs to access their own back-office, and you will need to make calls to different URLs to fetch their specific data.


Context
#######

Let's put ourselves in the shoes of a recent Homeloc partner, called NETSPI. NETSPI made a deal to take bookings for rentals published on one of Homeloc's booking websites: `La Cabane en l'air <http://www.lacabaneenlair.com/>`_.

NETSPI received credentials to access the API (``NETSPI/someVery$ecurep4ssword``) and was given an origin name for the booking they will create (``Centrale LaCabane - NETSPI``).


Importing rental data
#####################

NETSPI wants to import two types of information:

* The editorial content for each rental, that they will show to their potential customers on their website

* The booking information for each rental, that they will both show on their websites and store on their platform far later use. In particular, they want to import the rentals' availabilities, to be able to display a calendar on their website.

To do this, NETSPI makes API calls to the booking website rental api endpoint (more info :doc:`here <rental>`):
 ::

    curl 'http://www.lacabaneenlair.com/api/rentals/' --user NETSPI:someVery$ecurep4ssword

The data gathered is then stored on NETSPI infrastructure. In particular, they pay particular attention to the ``sub_instance_url`` field for each rental as it will be useful later on.


Checking the availability in real time
######################################

The availabilities imported in the previous step are not guaranteed to be fresh. Thus, when a customer on their website wants to check the availability for a potential stay, NETSPI needs to call our API. **This call needs to be made on the particular rental's sub-instance**, as fetched in the rental API.
 ::

    curl 'https://subinstance.lacabanenetwork.homelocpay.net/en/bookingengine/check_availability/' \
        --data start=17/09/2015 \
        --data end=18/09/2015 \
        --data rental=6 \
        --data-urlencode origin="Centrale LaCabane - NETSPI"


Note that this API also provides you with a price estimate (more info :doc:`here <availability_check>`)


Getting a definitive price
##########################

Once the customer is in their booking funnel, NETSPI now knows how many guests, and their eventual birth dates, the stay will be fore. They are now able to request a definitive price for the stay. Once again, this call is made on the rental's sub-instance.
 ::

    curl 'https://subinstance.lacabanenetwork.homelocpay.net/en/bookingengine/check_price/'
        --data start=17/09/2015 \
        --data end=18/09/2015 \
        --data rental=6 \
        --data birth_dates=,

More info on this call can be found :doc:`here <price_check>`


Registering the booking on Homeloc's platform
#############################################

After having taken care of the payment, NETSPI needs to push the booking to the Homeloc's infrastructure, to update the rental's availabilities. Once again, this call is made on the rental's sub-instance.
 ::

    curl 'https://subinstance.lacabanenetwork.homelocpay.net/en/bookingengine/create_booking/' \
        --user NETSPI:someVery$ecurep4ssword \
        --data start=17/09/2015 \
        --data end=18/09/2015 \
        --data rental=6 \
        --data-urlencode origin="Centrale LaCabane - NETSPI" \
        --data tenant_firstname=John \
        --data tenant_lastname=Watson \
        --data tenant_birth_date=1980-01-01 \
        --data guests_firstname=Sherlock \
        --data guests_lastname=Holmes \
        --data guests_birthdates=1980-01-01

When a booking is created on Homeloc's platform, this API calls return a booking ID, that will be needed to cancel the booking (more info :doc:`here <booking>`)


Cancellation of the booking
###########################

If the need arises, you can cancel a booking you created via the API. This call is also made on the rental's sub-instance.
 ::

    curl 'https://subinstance.lacabanenetwork.homelocpay.net/en/bookingengine/cancel_booking/' \
        --user NETSPI:someVery$ecurep4ssword \
        --data-urlencode origin="Centrale LaCabane - NETSPI" \
        --data reservation=221

More info on this call can be found :doc:`here <cancellation>`
