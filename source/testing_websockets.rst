Testing WebSockets
******************

Set the ``"engine"`` attribute of a scenario definition to ``"ws"`` to use WebSockets (the default engine is HTTP).

Two kinds of actions are supported: ``send`` and ``think``.

The underlying WebSocket client can be configured with a ``"ws"`` section in the ``"config"`` section of your test script. For a list of available options, please see `WS library docs <https://github.com/websockets/ws/blob/master/doc/ws.md#new-wswebsocketaddress-protocols-options>`_.

Example
#######

::

    {
      "config": {
          "target": "wss://echo.websocket.org",
          "phases": [
            {"duration": 20, "arrivalRate": 10}
          ],
          "ws": {
            "rejectUnauthorized": false // Ignore SSL certificate errors - can be useful in *development* with self-signed certs
          }
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
