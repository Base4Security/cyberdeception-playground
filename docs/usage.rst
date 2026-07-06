.. _usage:

Usage
=====

Parameters
----------

The startup script accepts one argument: the **deception level**.

.. list-table::
   :header-rows: 1
   :widths: 15 85

   * - Parameter
     - Description
   * - ``none``
     - No deception; minimal services only.
   * - ``basic``
     - Basic deception: SSH honeypot, fake credentials, decoy files.
   * - ``complete``
     - Full deception: basic + fake activity generator, decoy API endpoints, monitored DB columns.
   * - ``impossible``
     - Maximum deception: complete + modified banners/services, anti-forensics, tampered executables.

Levels vs deployed activities
------------------------------

.. list-table::
   :header-rows: 1
   :widths: 40 10 10 12 12

   * - Activity / Component
     - None
     - Basic
     - Complete
     - Impossible
   * - SSH decoy (honeypot)
     - No
     - Yes
     - Yes
     - Yes
   * - Fake credentials in honeypot user database
     - No
     - Yes
     - Yes
     - Yes
   * - Decoy files on frontend
     - No
     - Yes
     - Yes
     - Yes
   * - Fake activity generator
     - No
     - No
     - Yes
     - Yes
   * - Decoy API endpoints in backend
     - No
     - No
     - Yes
     - Yes
   * - Additional DB columns (monitored)
     - No
     - No
     - Yes
     - Yes
   * - Modified banners and "installed" services
     - No
     - No
     - No
     - Yes
   * - Forced uninstall of recent installation
     - No
     - No
     - No
     - Yes
   * - Modified key executables
     - No
     - No
     - No
     - Yes

Usage examples
--------------

**Example 1 — Start with complete deception**

.. code-block:: bash

   ./scripts/startup.sh complete
   # Wait for services; then open http://localhost:3000 and http://localhost:5601

**Example 2 — Access the web portal and Kibana**

- **Andesfinance Portal**: ``http://localhost:3000`` — credentials ``admin`` / ``admin123``
- **Kibana**: ``http://localhost:5601`` — use "Discover" for events

**Example 3 — Run automated attacks from the attacker container**

.. code-block:: bash

   docker exec -it attacker-tools /bin/bash
   cd attack_scripts/
   python3 main_attacker.py

**Example 4 — Manual command-injection test (from attacker container)**

.. code-block:: bash

   docker exec -it attacker-tools /bin/bash
   curl -X POST http://frontend:3000/diagnostics -H "Content-Type: application/json" \
     -d '{"system_check": "ping", "target_host": "localhost | hostname"}'

**Example 5 — Shut down (Windows)**

.. code-block:: batch

   .\scripts\shutdown.bat

Kibana visualization
--------------------

The dashboard includes:

- **Events by Type**: Distribution of event types
- **Real-time Activity**: Activity timeline
- **Source IPs**: Top IPs attempting access
- **Executed Commands**: Most frequent commands
- **Detailed Logs**: Detailed view of all events
