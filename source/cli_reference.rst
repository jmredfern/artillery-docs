CLI Reference
*************

The ``artillery`` command has several sub-commands:
::

  Usage: artillery [options] [command]


  Commands:

    run [options] <script>  Run a test script. Example: `artillery run benchmark.json`
    quick [options] <url>   Run a quick test without writing a test script
    report <file>           Create a report from a JSON file created by "artillery run"
    convert <file>          Convert JSON to YAML and vice versa

  Options:

    -h, --help     output usage information
    -V, --version  output the version number


Some commands take further options:

``run``
#######

Options for `run`:
::

    -o, --output <string>       Set filename of the JSON file with stats
    -k, --insecure              Turn off TLS certificate verification
    -e, --environment <string>  Set the environment to use (environments can be specified in the test file)
    -t, --target <string>       Set target server (overriding the one specified in the test file)
    -q, --quiet                 Turn on quiet mode (print nothing to stdout)

``quick``
#########

Use this command to run a quick test without needing to write a scenario. Example:

``artillery quick -d 60 -r 10 -k https://myapp.dev``

Options for `quick`:
::

    -d, --duration <seconds>     Set duration (in seconds)
    -r, --rate <number>          Set arrival rate (per second)
    -p, --payload <string>       Set payload (POST request body)
    -t, --content-type <string>  Set content-type
    -o, --output <string>        Set output filename
    -k, --insecure               Turn off TLS certificate verification
    -q, --quiet                  Turn on quiet mode
