************************************
Custom KLG API
************************************


1. Get Duplicate Legacy Members
====================================
| URL endpoint: https://stamps.co.id/api/legacy/members/check-duplicates-with-pin
| Allowed Method: GET
| Require Authentication: Yes

A. Request
-----------------------------
You can get unmerged legacy memberships that are potentially duplicate to a legacy member with this API.

============     =========== =========================
Parameter        Required    Description
============     =========== =========================
token            Yes         Authentication token in string
user             Yes         Legacy membership's email or mobile number
pin              Yes         Legacy mebership's PIN
============     =========== =========================


Example of API call request using cURL

.. code-block :: bash

    $ curl 'https://stamps.co.id/api/legacy/check-duplicates-with-pin?token=123&user=example@stamps.com&pin=123456'


B. Response
-----------

In response to this API call, Stamps will return response with the following data (in JSON):

=================== ==============================
Variable            Description
=================== ==============================
legacy_members      An array of legacy member objects
errors              Errors encountered when processing request (if any)
=================== ==============================


C. Example Response
-------------------

.. code-block :: bash

    HTTP/1.0 200 OK
    Vary: Accept
    Content-Type: application/json
    Allow: GET
      [Redacted Header]

    {
      "legacy_members": [
        {
          "member_id": "322145",
          "email": "legacy_member@stamps.com",
          "mobile_number": "+62851111222333",
          "merchant": 1,
          "status": 1
        },
        {
          "member_id": "63414",
          "email": "duplicate_member2@stamps.com",
          "mobile_number": "+62851111222444",
          "merchant": 2,
          "status": 1
        },
      ]
    }

2. Merge Legacy Membership Without Pin
====================================
| URL endpoint: https://stamps.co.id/api/legacy/members/merge-without-pin
| Allowed Method: POST
| Require Authentication: Yes

A. Request
-----------------------------
You can merge a legacy membership to a stamps membership with this API

================ =========== =========================
Parameter        Required    Description
================ =========== =========================
token            Yes         Authentication string
target_user      Yes         A string indicating customer's email, Member ID, mobile number or primary key ID
legacy_member    Yes         A string indicating legacy member's ID, mobile number or email
merchant_id      Yes         Merchant ID the legacy member is associated with
bonus_stamps     No          Integer, bonus points given to target user's membership
================ =========== =========================


Example of API call request using cURL

.. code-block :: bash

    $ curl -X POST -H "Content-Type: application/json" https://stamps.co.id/api/legacy/members/merge -i -d '{ "token": "secret", "target_user": 1, "legacy_member": 31245, "merchant_id": 1, "bonus_stamps": 10 }'



B. Response
-----------

In response to this API call, Stamps will return response with the following data (in JSON):

=================== ==============================
Variable            Description
=================== ==============================
membership          Various information about target user's membership
errors              Errors encountered when processing request (if any)
=================== ==============================


C. Example Response
-------------------

.. code-block :: bash

    HTTP/1.0 200 OK
    Vary: Accept
    Content-Type: application/json
    Allow: POST, OPTIONS
     [Redacted Header]

    {
      "membership": {
        "level": 100,
        "level_text": "Blue",
        "stamps": 410,
        "balance": 150000,
        "is_blocked": false,
        "referral_code": "ABCDE",
        "start_date": "2014-08-08",
        "created": "2014-08-08",
      }
    }

3. Activate Legacy Membership Without Pin
====================================
| URL endpoint: https://stamps.co.id/api/legacy/members/activate-without-pin
| Allowed Method: POST
| Require Authentication: Yes

A. Request
-----------------------------
This API turns a legacy member data into to an active membership.

================ =========== =========================
Parameter        Required    Description
================ =========== =========================
token            Yes         Authentication string
user             Yes         A string indicating legacy member's ID, mobile number or email
merchant_id      Yes         Merchant ID the legacy member is associated with
bonus_stamps     No          Integer, bonus points given to target user's membership
================ =========== =========================


Example of API call request using cURL

.. code-block :: bash

    $ curl -X POST -H "Content-Type: application/json" https://stamps.co.id/api/legacy/members/activate-without-pin -i -d '{ "token": "secret", "user": 12, "merchant_id": 1, "bonus_stamps": 10 }'



B. Response
-----------

In response to this API call, Stamps will return response with the following data (in JSON):

=================== ==============================
Variable            Description
=================== ==============================
user                Customer profile data
membership          Various information about active membership
errors              Errors encountered when processing request (if any)
=================== ==============================


C. Example Response
-------------------

.. code-block :: bash

    HTTP/1.0 200 OK
    Vary: Accept
    Content-Type: application/json
    Allow: POST, OPTIONS
     [Redacted Header]

    {
      "user": {
        "id": "123",
        "name": "Customer",
        "gender": "m",
        "address": "Jl MK raya",
        "is_active": true,
        "email": "customer@stamps.co.id",
        "phone": "+62812398712",
        "picture_url": "https://media.stamps.co.id/thumb/profile_photos/2014/4/17/483ccddd-9aea-44d2-bbc4-6aa71f51fb2a_size_80.png",
        "birthday": "1989-10-1",
      },
      "membership": {
        "level": 1,
        "level_text": "Blue",
        "stamps": 100,
        "balance": 0,
        "is_blocked": false,
        "referral_code": "abc123",
        "start_date": "2022-01-01",
        "created": "2022-01-01",
        "primary_card": {
          "id": 1,
          "number": "RRR123456",
          "is_active": true,
          "activated_time": "2022-01-20 10:00:00"
        }
      }
    }



4. Return Transaction Preview
=======================================
| URL endpoint: https://stamps.co.id/api/returns/preview
| Allowed Method: POST
| Require Authentication: Yes

A. Request
-----------------------------
Preview a return from a transaction.

============================== =========== =============================================================================
Parameter                      Required    Description
============================== =========== =============================================================================
root_invoice_number            Yes         Invoice number of the first original transaction
root_transaction_store         Yes         Store's id or code where the the first transaction happens
root_invoice_date              Yes         First transaction creation date in YYY-mm-dd format(e.g: 2022-08-30)
return_invoice_number          Yes         Invoice number for return transaction
return_created_datetime        No          When the return happens in ISO 8601 format(e.g: 2013-01-15T20:01:01+07).
                                           Default to now
return_store                   Yes         Store's id or code where the return happens
subtotal_delta                 No          Must be provided if the original transaction has subtotal
total_value_delta              Yes         The delta value of transaction's grand total after returned
payments                       No          Must be provided if original transaction has payments.
                                           Payments are list of :ref:`payment objects <Payment Object>`
stamps_to_add                  No          Stamps to be added by this transaction. If specified, this overrides system's calculation of the number of Stamps that will be added or deducted from this transaction.
stamps_to_deduct               No          Stamps to be deducted manually. If specified, this overrides the number of Stamps that will be deducted from this return.
                                           Can't be sent alongside stamps_to_add.
items                          Yes         Which items are returned. Items are list of :ref:`item objects<Item Object>`
============================== =========== =============================================================================

Example of API call request using cURL

.. code-block :: bash

    curl --location --request POST 'https://stamps.co.id/api/returns/preview' \
    --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNjYxOTMwNjg2LCJpYXQiOjE2NjE4NDQyODYsImp0aSI6IjZlM2U0ZGU0MzZkYzRjNDZhNGJhMjRkZWE2MjM0N2VjIiwidXNlcl9pZCI6MSwibWVyY2hhbnRfaWQiOjF9.brgNBzeuPmOV6ECP5WpwJJlQ6MQZ1zACHYx1YiW33AM' \
    --header 'Content-Type: application/json' \
    --header 'Cookie: csrftoken=FAc0E8TCQSCqCKhNNH62Pr3KTFgfemz2DMPWkjdSkD68VJYKda38emJi8GykuSgd; sessionid=sl07y2ektnrikw4bddkr4kndr482qms4' \
    --data-raw ' {
                "root_invoice_number": "6288988812712621",
                "root_transaction_store": 3,
                "root_invoice_date": "2022-08-30",
                "return_invoice_number": "6288988812712621.1",
                "total_value_delta": -1,
                "return_store": 3,
                "items": [
                    {
                        "product_name": "tea",
                        "quantity": -1,
                        "subtotal": -15000
                    }
                ],
                "payments": [
                    {
                        "payment_method": "1300",
                        "value": -15000
                    }
                ]
            }'

B. Response Data
----------------

Stamps responds to this API call with the following data (in JSON):

==================== ===========================================================================
Variable             Description
==================== ===========================================================================
user                 Information about this transaction's user
membership           Information about the user's membership on the transaction's merchant
root_transaction     Information about the first original transaction
original_transaction Information about the previous transaction
modified_transaction Information about the new transaction after return happens
modication           Information about the modified data of the original transaction
==================== ===========================================================================


C. Response Headers
-------------------

=================== =======================================================================
Code                Description
=================== =======================================================================
200                 Everything worked as expected
400                 Bad Request - Often missing a
                    required parameter
401                 Unauthorized – Often missing or
                    wrong authentication token
403                 Forbidden – You do not have
                    permission for this request
405                 HTTP method not allowed - The
                    requested resources cannot be called with the specified HTTP method
500, 502, 503, 504  Server Errors - something is
                    wrong on Stamps' end
=================== =======================================================================


D. Examples
-----------

On a successful API call:

.. code-block :: bash

    {
        "user": {
            "id": "2845532",
            "name": "Marsha Test",
            "gender": "male",
            "address": "",
            "is_active": true,
            "email": "marshadouble@test.com",
            "picture_url": null,
            "birthday": "1988-04-23",
            "phone": "+628898881212",
            "postal_code": "",
            "protected_redemption": false,
            "has_incorrect_email": false,
            "marital_status": null,
            "religion": null,
            "wedding_date": null,
            "id_number": null,
            "id_card_file_name": "",
            "phone_is_verified": false,
            "email_is_verified": false
        },
        "membership": {
            "tags": [],
            "status": 100,
            "status_text": "Blue",
            "stamps": 13.0,
            "balance": 0,
            "is_blocked": false,
            "referral_code": "AL6J3A9",
            "start_date": "2022-08-03",
            "created": "2022-08-03"
        },
        "root_transaction": {
            "id": 26091,
            "value": 150000.0,
            "stamps_earned": 25,
            "number_of_people": null,
            "discount": null,
            "subtotal": null,
            "items": [
                {
                    "id": 3670,
                    "quantity": 10.0,
                    "subtotal": 150000.0,
                    "price_per_unit": null,
                    "product": {
                        "id": 3,
                        "name": "tea"
                    }
                }
            ],
            "payments": [
                {
                    "id": 78,
                    "value": 150000.0,
                    "eligible_for_stamps": true,
                    "payment_method_code": "1300"
                }
            ]
        },
        "original_transaction": {
            "id": 26091,
            "value": 150000.0,
            "stamps_earned": 25,
            "number_of_people": null,
            "discount": null,
            "subtotal": null,
            "items": [
                {
                    "id": 3670,
                    "quantity": 10.0,
                    "subtotal": 150000.0,
                    "price_per_unit": null,
                    "product": {
                        "id": 3,
                        "name": "tea"
                    }
                }
            ],
            "payments": [
                {
                    "id": 78,
                    "value": 150000.0,
                    "eligible_for_stamps": true,
                    "payment_method_code": "1300"
                }
            ]
        },
        "modified_transaction": {
            "id": null,
            "value": 149999.0,
            "stamps_earned": 13,
            "number_of_people": null,
            "discount": null,
            "subtotal": 135000.0,
            "items": [],
            "payments": [
                {
                    "id": null,
                    "value": 135000.0,
                    "eligible_for_stamps": true,
                    "payment_method_code": "1300"
                }
            ]
        },
        "modification": {
            "id": null,
            "created": 1661844369,
            "stamps_delta": -12.0,
            "subtotal_delta": -15000.0
        }
    }

On an invalid request:

.. code-block :: bash

    {
        "detail": "root_transaction_store: No store with given identifier",
        "error_message": "root_transaction_store: No store with given identifier",
        "error_code": "invalid_store",
        "errors": {
            "root_transaction_store": "No store with given identifier"
        }
    }


5. Add a return transaction
=======================================
| URL endpoint: https://stamps.co.id/api/returns/add
| Allowed Method: POST
| Require Authentication: Yes

A. Request
-----------------------------

============================== =========== ==================================================================================================================
Parameter                      Required    Description
============================== =========== ==================================================================================================================
root_invoice_number            Yes         Invoice number of the first original transaction
root_transaction_store         Yes         Store's id or code where the the first transaction happens
root_invoice_date              Yes         First transaction creation date in YYY-mm-dd format(e.g: 2022-08-30)
return_invoice_number          Yes         Invoice number for return transaction
return_created_datetime        No          When the return happens in ISO 8601 format(e.g: 2013-01-15T20:01:01+07).
                                           Default to now
return_store                   Yes         Store's id or code where the return happens
subtotal_delta                 No          Must be provided if the original transaction has subtotal
total_value_delta              Yes         The delta value of transaction's grand total after returned
payments                       No          Must be provided if original transaction has payments.
                                           Payments are list of :ref:`payment objects <Payment Object>`
items                          Yes         Which items are returned. Items are list of :ref:`item objects<Item Object>`
stamps_to_add                  No          Stamps to be added by this transaction. If specified, this overrides system's calculation of the number of Stamps that will be added or deducted from this transaction.
stamps_to_deduct               No          Stamps to be deducted manually. If specified, this overrides the number of Stamps that will be deducted from this return.
                                           Can't be sent alongside stamps_to_add.
cancel_redemptions             No          Also cancel redemptions related to original transaction. Default to "false"
============================== =========== ==================================================================================================================

Example of API call request using cURL

.. code-block :: bash

    curl --location --request POST 'https://stamps.co.id/api/returns/add' \
    --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNjYxOTMwNjg2LCJpYXQiOjE2NjE4NDQyODYsImp0aSI6IjZlM2U0ZGU0MzZkYzRjNDZhNGJhMjRkZWE2MjM0N2VjIiwidXNlcl9pZCI6MSwibWVyY2hhbnRfaWQiOjF9.brgNBzeuPmOV6ECP5WpwJJlQ6MQZ1zACHYx1YiW33AM' \
    --header 'Content-Type: application/json' \
    --header 'Cookie: csrftoken=FAc0E8TCQSCqCKhNNH62Pr3KTFgfemz2DMPWkjdSkD68VJYKda38emJi8GykuSgd; sessionid=sl07y2ektnrikw4bddkr4kndr482qms4' \
    --data-raw ' {
                "root_invoice_number": "6288988812712621",
                "root_transaction_store": 3,
                "root_invoice_date": "2022-08-30",
                "return_invoice_number": "6288988812712621.1",
                "total_value_delta": -1,
                "return_store": 3,
                "items": [
                    {
                        "product_name": "tea",
                        "quantity": -1,
                        "subtotal": -15000
                    }
                ],
                "payments": [
                    {
                        "payment_method": "1300",
                        "value": -15000
                    }
                ]
            }'

B. Response Data
----------------

Stamps responds to this API call with the following data (in JSON):

==================== ===========================================================================
Variable             Description
==================== ===========================================================================
user                 Information about this transaction's user
membership           Information about the user's membership on the transaction's merchant
root_transaction     Information about the first original transaction
original_transaction Information about the previous transaction
modified_transaction Information about the new transaction after return happens
modication           Information about the modified data of the original transaction
==================== ===========================================================================


C. Examples
-----------

On a successful API call:

.. code-block :: bash

    {
        "user": {
            "id": "2845532",
            "name": "Marsha Test",
            "gender": "male",
            "address": "",
            "is_active": true,
            "email": "marshadouble@test.com",
            "picture_url": null,
            "birthday": "1988-04-23",
            "phone": "+628898881212",
            "postal_code": "",
            "protected_redemption": false,
            "has_incorrect_email": false,
            "marital_status": null,
            "religion": null,
            "wedding_date": null,
            "id_number": null,
            "id_card_file_name": "",
            "phone_is_verified": false,
            "email_is_verified": false
        },
        "membership": {
            "tags": [],
            "status": 100,
            "status_text": "Blue",
            "stamps": 13.0,
            "balance": 0,
            "is_blocked": false,
            "referral_code": "AL6J3A9",
            "start_date": "2022-08-03",
            "created": "2022-08-03"
        },
        "root_transaction": {
            "id": 26091,
            "value": 150000.0,
            "stamps_earned": 25,
            "number_of_people": null,
            "discount": null,
            "subtotal": null,
            "items": [
                {
                    "id": 3670,
                    "quantity": 10.0,
                    "subtotal": 150000.0,
                    "price_per_unit": null,
                    "product": {
                        "id": 3,
                        "name": "tea"
                    }
                }
            ],
            "payments": [
                {
                    "id": 78,
                    "value": 150000.0,
                    "eligible_for_stamps": true,
                    "payment_method_code": "1300"
                }
            ]
        },
        "original_transaction": {
            "id": 26091,
            "value": 150000.0,
            "stamps_earned": 25,
            "number_of_people": null,
            "discount": null,
            "subtotal": null,
            "items": [
                {
                    "id": 3670,
                    "quantity": 10.0,
                    "subtotal": 150000.0,
                    "price_per_unit": null,
                    "product": {
                        "id": 3,
                        "name": "tea"
                    }
                }
            ],
            "payments": [
                {
                    "id": 78,
                    "value": 150000.0,
                    "eligible_for_stamps": true,
                    "payment_method_code": "1300"
                }
            ]
        },
        "modified_transaction": {
            "id": 26092,
            "value": 149999.0,
            "stamps_earned": 13,
            "number_of_people": null,
            "discount": null,
            "subtotal": 135000.0,
            "items": [],
            "payments": [
                {
                    "id": 79,
                    "value": 135000.0,
                    "eligible_for_stamps": true,
                    "payment_method_code": "1300"
                }
            ]
        },
        "modification": {
            "id": 1,
            "created": 1661844369,
            "stamps_delta": -12.0,
            "subtotal_delta": -15000.0
        }
    }

On an invalid request:

.. code-block :: bash

    {
        "detail": "root_transaction_store: No store with given identifier",
        "error_message": "root_transaction_store: No store with given identifier",
        "error_code": "invalid_store",
        "errors": {
            "root_transaction_store": "No store with given identifier"
        }
    }


Miscellaneous
------------------------------

Payment Object
^^^^^^^^^^^^^^
============================== =========== ===================================================================
Parameter                      Required    Description
============================== =========== ===================================================================
payment_method                 Yes         Payment method code
value                          Yes         Nominal of payment, must be negative
============================== =========== ===================================================================

Item Object
^^^^^^^^^^^
============================== =========== ===================================================================
Parameter                      Required    Description
============================== =========== ===================================================================
product_name                   Yes         Product name of the item
quantity                       Yes         Returned quantity, must be negative
subtotal                       Yes         Returned subtotal, must be negative
============================== =========== ===================================================================
