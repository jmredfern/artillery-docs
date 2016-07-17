Examples
********

Debugging your scripts
######################

If you'd like to see the details of every request that Artillery is sending, run it like:
::

  DEBUG=http artillery run myscript.yaml


If you'd like to see every response that Artillery receives from the server, run with:
::

  DEBUG=http:response artillery run myscript.yaml

Randomizing requests
####################

Artillery's scenarios have the concept of *variables*, which can be used in strings with `{{ varName }}` / `{{{ varName }}}` (e.g. in URLs, or POST body payloads). There are three ways to create a variable:

- By capturing part of a response (see "Parsing JSON Responses" below)
- By importing values from external CSV files
- By specifying values inline in the test script

Using CSV files
~~~~~~~~~~~~~~~

``config.payload`` can be used to import values from one or more external CSV files:
::

    {
      "config": {
        // ... other config here ...
        "payload": {
          "path": "./relative/path/to/mydata.csv",
          "fields": ["name", "houseNumber", "street", "zipCode"]
        }
      }
    }

Here we have defined 4 variables (``name``, ``houseNumber``, ``street``, and ``zipCode``) which would correspond to the fields in the CSV file.

When a virtual user is launched and picks a scenario where these variables are used, a line from the CSV file will be read at random to populate the variables.

To tell Artillery to read the lines in sequence, set ``order`` attribute to ``sequence``:
::

    "payload": {
      "path": "./relative/path/to/mydata.csv",
      "fields": ["name", "houseNumber", "street", "zipCode"],
      "order": "sequence"
    }

Using inline variables
~~~~~~~~~~~~~~~~~~~~~~

``config.variables`` can be used to specify variable values inline in the script like so:
::

    {
      "config": {
        // ... other config here ...

        "variables": {
          "zipCode": ["35801", "99501", "85001", "94203", "90210"]
        }
      }
    }

Here we have defined a ``zipCode`` variable which will have the value picked at random from the list when a virtual user executes the scenario where the variable is used.

The variable can be used like so:
::

    {
      "config: {
        // ... config here ...
      },
      "scenarios": [
        {
          "flow": [
            {"get": {"url": "/zip/{{ zipCode }}"}}
          ]
        }
      ]
    }
