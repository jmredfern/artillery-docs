Script Reference
****************

Artillery runs scripts written as JSON or YAML files which configure the target and any default behavior, and the scenarios for virtual users that Artillery will generate.

An Artillery script is made up of ``config`` and ``scenarios`` sections.

``config``
##########

- ``target`` - base URL for all requests in this script. Examples: ``http://myapp.staging.local`` or ``ws://127.0.0.1``.
- ``environments`` - specify a list of environments, and associated target URLs; see below
- ``phases`` - specify the duration of the test and frequency of requests; see below
- ``payload`` - used for importing variables from a CSV file; see below
- ``defaults`` - set default headers that will apply to all HTTP requests
- ``ensure`` - cause Artillery to return with a non-zero error code under certain result conditions. Useful for CI
- ``tls`` - configure how Artillery handles self-signed certificates. See [HTTP Reference](/docs/http-reference.html)
- ``timeout`` - number of seconds to wait for the server to start responding (send response headers and start the response body). Default value is ``10``.

Phases
~~~~~~

A phase is a period of the load test that can be named and have timing parameters. This can be used to do things like having a ramp-up phase following by a more intense phase.

``config.phases`` is an array of phase definitions that Artillery goes through sequentially. Three kinds of phases are supported:

- ``arrivalRate`` - specify the arrival rate of virtual users for a duration of time.
  - A linear "ramp" in arrival can be also be created with the ``rampTo`` option
- ``arrivalCount`` - specify the number of users to create over a period of time.
- ``pause`` - pause and do nothing for a duration of time.

**Examples:**

Create 50 virtual users every second (on average) for 5 minutes:
::

    { "duration": 300, "arrivalRate": 50 }

Ramp up arrival rate from 10 to 50 over 2 minutes:
::

    { "duration": 120, "arrivalRate": 10, "rampTo": 50 }


Create 20 virtual users in 60 seconds (one every 3 seconds on average)
::

    { "duration": 60, "arrivalCount": 20 }


Pause for 10 seconds between arrival phases:
::

    { "pause": 10 }


Arrival phases can be named, for example:
::

    { "duration": 180, "arrivalRate": 10, "name": "Warm-up" }


Putting it all together:
::

    {
      "config": {
        "target": "http://myapp.staging.local",
        "phases": [
          {"duration": 300, "arrivalRate": 5, "name": "Warm-up"},
          {"pause": 10},
          {"duration": 60, "arrivalCount": 30 },
          {"duration": 600, "arrivalRate": 50, "name": "High load phase"}
        ]
      },
      "scenarios": [
        // scenario definitions
      ]
    }


Environments
~~~~~~~~~~~~

You can specify a number of named environments with associated configuration. E.g.:
::

    {
      "config": {
          "target": "http://wontresolve.local:3003",
          "phases": [
            {"duration": 10, "arrivalRate": 1}
          ],
          "environments": {
            "production": {
              "target": "http://wontresolve.prod:44321"
            },
            "staging": {
              "target": "http://127.0.0.1:3003",
              "phases": [
                {"duration": 20, "arrivalRate": 1}
              ]
            }
          }
      },
      "scenarios": [
         ...

Choose an environment on the command line with the ``-e`` argument; e.g. ``-e staging``.

Payloads
~~~~~~~~

In some cases it is useful to be able to inject data from external files into your test scenarios. For example, you might have a list of usernames and passwords that you want to use to test the auth endpoint in your API.

Payload files are in the CSV format and Artillery allows you to map each of the rows to a variable name that can be used in scenario definitions. For example:
::

    {
      "config": {
        // other config...
        "payload": {
          "path": "users.csv", // path is relative to the location of the test script
          "fields": ["username", "password"]
        }
      },
      "scenarios": [
        {
          "post": {
          "url": "/auth",
          "json": {
            "username": "{{ username }}",
            "password": "{{ password }}"
          }
         }
        }
      ]
    }


To use multiple CSV files ``"payload"`` can also be an an array:

::

    "payload": [
      {
        "path": "./pets.csv",
        "fields": ["species", "name"]
      },
      {
        "path": "./urls.csv",
        "fields": ["url"]
      }
    ]
    

Ordering
--------

Rows from the CSV file are picked *at random* by default. To iterate through the rows in sequence (looping around and starting from the beginning after the last row has been reached), set the ``"order"`` attribute to ``"sequence"``:
::

    {
      "config": {
        // other config...
        "payload": {
          "path": "users.csv", // path is relative to the location of the test script
          "fields": ["username", "password"],
          "order": "sequence"
        }
      },
      "scenarios": [
        // rest of the script

``scenarios``
#############

The ``scenarios`` key is an array that must exist in the root of the script. It contains a list of ``flow`` objects.

A scenario is a sequence of steps that need to be run sequentially, and represents what a sequence of calls generated by a simulated user.

``name``
~~~~~~~~

You can give your scenario a descriptive name with this attribute, e.g. ``"search for a product and get its details"``

``weight``
~~~~~~~~~~

Weights allow you to specify that some scenarios should be picked more often than others. If you have three scenarios with weights ``1``, ``2``, and ``5``, the scenario with the weight of ``2`` is twice as likely to be picked as the one with weight ``1``, and 2.5 times less likely than the one with weight ``5``. Or in terms of probabilities:

- scenario 1: 1/8 = 12.5% probability of being picked
- scenario 2: 2/8 = 25% probability
- scenario 3: 5/8 = 62.5% probability

Weights are optional, and if not specified are set to ``1`` (so each scenario is equally likely to be picked).

``flow``
~~~~~~~~

A "flow" is an array of operations that a virtual user performs, e.g. GET and POST requests for an `HTTP <testing_http.html>`_ scenario.

``think``
---------

You can use a ``think`` step in a flow to pause the execution of the scenario for N seconds, e.g.:
::

    { "think": 1 }

will pause for 1 second before continuing with the next request.
