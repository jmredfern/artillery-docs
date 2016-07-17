Testing WebSockets
******************

Set the ``"engine"`` attribute of a scenario definition to ``"ws"`` to use WebSockets (the default engine is HTTP).

Two kinds of actions are supported: ``send`` and ``think``.

Example
#######

::

    {
      "config": {
          "target": "ws://echo.websocket.org",
          "phases": [
            {"duration": 20, "arrivalRate": 10}
          ]
      },
      "scenarios": [
        {
          "engine": "ws",
          "flow": [
            {"send": "hello"},
            {"think": 1},
            {"send": "world"}
          ]
        }
      ]
    }

Inline variables, variables from CSV files can be used just like in an `HTTP scenario <testing_http.html>`_.

The WebSocket engine does not support parsing and reusing responses yet.
