.. _attack-simulation:

Attack Simulation
=================

The **attacker** container includes recon and attack tools (nmap, curl, sshpass, Python scripts)
to simulate a realistic intrusion kill-chain against the Andesfinance environment.

Quick start
-----------

.. code-block:: bash

   # Enter the attacker container
   docker exec -it attacker-tools bash

   # Run the automated 8-phase attack
   cd attack_scripts/
   python3 main_attacker.py

The script is **interactive**: before each phase it asks ``[c]ontinue / [s]kip / [q]uit``,
so you can run the full chain or step through individual phases.
A complete run takes roughly **10–20 minutes** depending on network scan results.

See :doc:`usage` for manual attack examples (curl-based command injection, direct SSH brute force).

Attack phases
-------------

Each phase prints a MITRE ATT&CK banner before executing, e.g.::

   ============================================================
     MITRE ATT&CK  |  T1046
     Tactic        |  Discovery
     Technique     |  Network Service Scanning
   ============================================================

.. list-table::
   :header-rows: 1
   :widths: 10 18 15 55

   * - Phase
     - Component
     - ATT&CK ID
     - What it does
   * - 1
     - ``web_application_discovery.py``
     - T1595.003
     - Enumerates endpoints, forms, input fields, and JavaScript API calls. Flags sensitive inputs, admin panels, and SQL references.
   * - 2
     - ``command_injection.py``
     - T1059.004
     - Injects OS commands via the ``/diagnostics`` endpoint (e.g., ``| cat /etc/passwd``). Demonstrates shell access through a vulnerable ping utility.
   * - 3
     - ``sql_injection.py``
     - T1190
     - Attempts authentication bypass with OR-based payloads; follows with UNION SELECT to extract table names, column names, version, and user credentials from MySQL.
   * - 4
     - ``reconnaissance.py``
     - T1018
     - Discovers the frontend's internal IPs via ``ifconfig`` injection, then runs ``nmap -sn`` ping sweeps across the ``/24`` subnet to enumerate live hosts.
   * - 5
     - ``port_scanning.py``
     - T1046
     - Runs ``nmap -Pn -T4 --top-ports 100`` on each discovered host, parsing open TCP ports and service names.
   * - 6
     - ``ssh_bruteforce.py``
     - T1110.001
     - Iterates a username/password dictionary against hosts with port 22 open, using ``sshpass`` via the command-injection vector.
   * - 6.5
     - ``lateral_movement.py``
     - T1021.004
     - Reuses credentials obtained in Phase 6 to attempt direct ``paramiko`` SSH connections to all other discovered hosts. On success, runs a post-access recon command set (``whoami``, ``id``, ``uname -a``, ``ip addr``, ``ps aux``).
   * - 7
     - ``data_exfiltration.py``
     - T1041
     - Queries the exposed ``/query`` endpoint with raw SQL (``SELECT * FROM users``, ``SHOW TABLES``, ``SELECT DATABASE()``), extracting database contents directly.

Success criteria
----------------

After a full run, verify results in Kibana (``http://localhost:5601``):

- **Attack Timeline** dashboard — events should span all phases with MITRE tactic labels.
- **ATT&CK Technique Distribution** — pie chart should show at least 5 distinct technique IDs.
- **SSH Honeypot Activity** — brute-force attempts visible if the honeypot was reached.
- **Injection Attacks Detail** — SQL and command injection events with ``security.threat_detected: true``.

You can also inspect raw events in Discover using the filter ``security.threat_detected: true``.

Base class
----------

``base_attacker.py`` provides:

- ``MITRE_TTPS`` dict mapping phase keys to ATT&CK tactic, technique ID, and technique name.
- ``print_mitre_header(phase_key)`` — prints the ATT&CK banner before each phase.
- ``ask_continue(step_name)`` — interactive continue/skip/quit prompt.
- Shared ``requests.Session`` and structured ``logging`` for all modules.
