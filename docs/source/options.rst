Options to modify systemd DHCP client
=====================================

Options
-------

A.
~~

-  Add option UseAnonymityProfile
-  defaults to false
-  setting it to true override settings, even if they've been explicitly
   setup. Produce a warning about it
-  modifying as less as possible existing code.

B.
~~

-  Add option UseAnonymityProfile
-  defaults to false
-  variables hat are explicitly set would still take effect
-  unset variables would be controlled according to the AnonymityProfile

C.
~~

-  do not have UseAnonymityProfile variable
-  remove all the code that is not needed for the Anonymity Profiles
