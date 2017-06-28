Services
=========

Listing of services and what they do :)

idempotence_store
------------------

Stores idempotence ids for use by other services

- Replace the redis data-backend with something news


sms-inbound
-----------

Microservice for receiving inbound data from Twilio's SMS service

- Use secrets store for wrapping phonenumbers
- Add handling for delivery notifications


sms-outbound
-------------

Microservice for sending sms based on tasks

- Use secrets store for unwrapping phonenumbers


secrets-store
--------------

Service for wrapping secrets

- Make it actually work

se-id-check
------------

Service for performing the Swedish id-check

- Flip around the UC values mapping so it goes from ourFieldCode -> UCW0000
- Add support for data-types
- Add Redis support


Not yet built
==============

se-credit-check (name tbd)
---------------------------

Takes a Swedish UC of a person.

Design details
- Absolutely critical that UC are never taken for old events.
- Also critical that UCs are not taken more than once.

positor
---------

Service validating and writing events to the kafka log for cases.

- Validate according to schema
- Write to kafka queue

case-maker (name tbd)
---------------------
HTML web app for creating new cases


view-maker (name tbd)
----------------------
Reduces over queues making views and storing them in redis

- Runs on apache flink for stability and scalability
- Writes to Redis
- Triggers events for document viewer

doc-store (name tbd)
---------------------------

Exposes redis data, and applies access control rules. This redis data
has two types documents and feeds. Documents are entirely overwritten
on update, while feeds can be added to.

- Exposes data using a websocket
- Users subscribes to a document and a new copy gets pushed when it is updated


case-viewer (name tbd)
----------------------
HTML web-app

Shows a list of views (Today, Debt letter sent etc) as well as actual cases


partner-bid-ui (name tbd)
--------------------------
HTML web-app

GUI where partners can place bids on cases relevant to them.


scheduler
----------

Service that schedules an event to written to a log at a future
time. Uses idempotence ids so as to avoid double writes.

Schedule something:
- If the time is in the past, write it now
- Otherwise write it to a priority queue and wake the priority queue worker

Priority queue worker:
- Peek at the priority queue if top is now or in the past, write to log
- Sleep until the next item is due.

Open question: Can this run on multiple nodes?
