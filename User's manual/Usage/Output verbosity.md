## Output verbosity

Option: `-v`

This option can be used to set the verbosity level of output messages. There exist **seven** levels of verbosity. The default level is **1** in which information, warning, error, critical messages and Python tracebacks (if any occur) are displayed.

* **0**: Show only Python tracebacks, error and critical messages.
* **1**: Show also information and warning messages.
* **2**: Show also debug messages.
* **3**: Show also payloads injected.
* **4**: Show also HTTP requests.
* **5**: Show also HTTP responses' headers.
* **6**: Show also HTTP responses' page content.

A reasonable level of verbosity to further understand what sqlmap does under the hood is level **2**, primarily for the detection phase and the take-over functionalities. Whereas if you want to see the SQL payloads the tools sends, level **3** is your best choice. This level is also recommended to be used when you feed the developers with a potential bug report, make sure you send along with the standard output the traffic log file generated with option `-t`.
In order to further debug potential bugs or unexpected behaviours, we recommend you to set the verbosity to level **4** or above. It should be noted that there is also a possibility to set the verbosity by using the shorter version of this option where number of letters `v` inside the provided switch (instead of option) determines the verbosity level (e.g. `-v` instead of `-v 2`, `-vv` instead of `-v 3`, `-vvv` instead of `-v 4`, etc.)