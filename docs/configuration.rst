.. _configuration:

Configuration
=============

Customize fake activity
-----------------------

Edit ``fake-activity/app.py``:

.. code-block:: python

   # Add new users
   USERS = ["alice", "bob", "carol", "new_user"]

   # Add new commands
   COMMANDS = [
       "ls -la",
       "cat /etc/passwd",
       "new_interesting_command"
   ]

Configure more honeypots
------------------------

Add to ``docker-compose.yml``:

.. code-block:: yaml

   services:
     ssh-honeypot-2:
       build: ./ssh-honeypot
       ports:
         - "2224:22"
       # ... rest of configuration

Integrate with external SIEM
-----------------------------

Modify ``filebeat/filebeat.yml``:

.. code-block:: yaml

   output.elasticsearch:
     hosts: ["external-siem:9200"]
     # ... additional configuration
