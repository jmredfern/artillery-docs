Getting Started
***************

Installing Artillery
####################

Artillery is written in `Node.js <http://nodejs.org/>`_ (but you don't need to know Node or JS to use it). Once Node.js is installed, install Artillery with:
::

    npm install -g artillery

To check that the installation succeeded, run:
::

    artillery dino

If you see an ASCII dinosaur, the installation has been successful!

Run a quick test
################

Artillery has a ``quick`` command which allows you to use it for ad-hoc testing (in a manner similar to ``ab``). Run:
::

     artillery quick --duration 60 --rate 10 -n 20 http://my.app.dev/api/resource

To create 10 virtual users every second for 60 seconds which will send 20 ``GET`` requests each.

Run a test script
#################

While the ``quick`` command can be useful for simple ad-hoc tests, the full power of Artillery lies in being able to simulate realistic user behavior with scenarios. Let's run one.

Copy the following code into a ``hello.yml`` file:
::

    config:
      target: 'http://my.app.dev'
      phases:
        - duration: 60
          arrivalRate: 20
      defaults:
        headers:
          x-my-service-auth: '987401838271002188298567'

    scenarios:
      - flow:
        - get:
            url: "/api/resource"


And run it with:
::

    artillery run hello.yml

As Artillery runs the test, it will print various stats to the terminal (request latency, response codes etc).

What our test does
##################

In this script, we specify that we are testing a service running on ``http://my.app.dev`` which will be talking to over HTTP. We define one *load phase*, which will last 60 seconds with 20 new *virtual users* (arriving every second (on average).

Then we define one possible scenario for every new virtual user to pick from, which consists of one ``GET`` request.

We also set an ``x-my-service-auth`` header to be sent with every request.

Reading the output
##################

While Artillery is running, you will see something like this printed to the terminal:

::

    Scenarios launched:  5
    Scenarios completed: 5
    Requests completed:  58
    RPS sent: 0.86
    Request latency:
      min: 102.4
      max: 3067.5
      median: 325.5
      p95: 2118.5
      p99: 3020
    Scenario duration:
      min: 56745.4
      max: 67339.1
      median: 59275.6
      p95: NaN
      p99: NaN
    Codes:
      200: 58

While the test is running, **intermediate** stats will be printed every 10 seconds (by default) and a complete report will be printed at the end of the test.


- ``Scenarios launched`` is the number of virtual users created in the preceding 10 seconds (or in total for the final report)
- ``Scenarios completed`` is the number of virtual users that completed their scenarios in the preceding 10 seconds (or in the whole test). Note: this is the number of completed sessions, not the number of sessions started and completed in a 10 second interval.
- ``Requests completed`` is the number of HTTP requests and responses or WebSocket messages sent
- ``RPS sent`` is the average number of requests per second completed in the preceding 10 seconds (or throughout the test)
- ``Request latency`` and ``Scenario duration`` are in milliseconds, and p95 and p99 values are the 95th and 99th `percentile <https://en.wikipedia.org/wiki/Percentile>`_ values (a request latency ``p99`` value of 500ms means that 99 out of 100 requests took 500ms or less to complete).
- ``Codes`` is the count of HTTP response codes.

If there are any errors (such as socket timeouts), those will be printed under ``Errors`` in the report as well.
