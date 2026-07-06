Deception Levels
================

The Cyber Deception Playground supports four progressive deception levels, each adding more techniques to increase an attacker's decision-making cost. Levels are selected at startup and control which Docker Compose profiles activate and which database schema is loaded.

.. contents::
   :local:
   :depth: 2

----

Capability Overview
-------------------

.. list-table::
   :header-rows: 1
   :widths: 40 12 12 14 14

   * - Capability
     - none
     - basic
     - complete
     - impossible
   * - Production services (frontend, backend, MySQL)
     - ✅
     - ✅
     - ✅
     - ✅
   * - Elastic Stack monitoring (Filebeat, Elasticsearch, Kibana)
     - ✅
     - ✅
     - ✅
     - ✅
   * - Attacker container
     - ✅
     - ✅
     - ✅
     - ✅
   * - SSH Honeypot
     - ❌
     - ✅
     - ✅
     - ✅
   * - Fake credentials in userdb
     - ❌
     - ✅
     - ✅
     - ✅
   * - Decoy config files (``/config`` directory)
     - ❌
     - ✅
     - ✅
     - ✅
   * - Fake Activity Generator
     - ❌
     - ❌
     - ✅
     - ✅
   * - Decoy API endpoints / honeytokens
     - ❌
     - ❌
     - ✅
     - ✅
   * - Hidden HTML links injected into pages
     - ❌
     - ❌
     - ✅
     - ✅
   * - Extra decoy DB columns (``password2``, ``password_selector``)
     - ❌
     - ❌
     - ✅
     - ✅
   * - Exposed ``system_config`` table with API keys / SSH keys
     - ❌
     - ❌
     - ❌
     - ✅
   * - Fake ``file_system`` and ``network_info`` tables
     - ❌
     - ❌
     - ❌
     - ✅
   * - ``exposed_credentials`` SQL view (honeypot)
     - ❌
     - ❌
     - ❌
     - ✅
   * - 20+ decoy user accounts in database
     - ❌
     - ❌
     - ❌
     - ✅
   * - Modified service banners (T1592 misdirection)
     - ❌
     - ❌
     - ❌
     - ✅
   * - Anti-forensics indicators (T1070 detection)
     - ❌
     - ❌
     - ❌
     - ✅
   * - Tampered executables / decoy binaries (T1036.005 misdirection)
     - ❌
     - ❌
     - ❌
     - ✅

----

Level: none
-----------

The production baseline. No deception is active — the environment is vulnerable but not defended.

**What an attacker sees:**

- A standard financial web portal (Andesfinance) on port 3000.
- SQL injection and command injection vulnerabilities in the application.
- No SSH honeypot, no fake data, no decoy endpoints.
- Kibana and Elasticsearch available for monitoring (defender only).

**Purpose:** Establish the undeceived baseline. Useful for demonstrating what a successful attack looks like without defensive measures, and for measuring the attacker's time-to-exfiltration.

**Start command:**

.. code-block:: bash

   ./scripts/startup.sh none

----

Level: basic
------------

Adds the first deception layer: an SSH honeypot and fake credentials to detect and misdirect initial access attempts.

**What changes:**

- **SSH Honeypot** (``ssh-honeypot/``) starts listening on port 22. It accepts connections but logs every authentication attempt, command, and session to ``ssh_connections.json`` (shipped to Elasticsearch via Filebeat).
- **Fake user database** (``ssh-honeypot/config/userdb.txt``) contains low-entropy credentials (``root/toor``, ``admin/admin123``, ``backup/backup123``) that an attacker will discover during brute force and attempt to reuse.
- **Decoy config files** become accessible at ``/config``. The directory listing exposes files like ``aws-credentials.json``, ``database.conf``, and ``environment.env`` — all containing fictitious but realistic-looking secrets.

**What an attacker sees:**

- An SSH service that accepts their credentials and appears functional.
- Config files with credentials that look valid and tempt lateral movement.
- More activity in Elasticsearch as the honeypot logs every connection attempt.

**Purpose:** Demonstrate the simplest deception — a single layer of misdirection that immediately surfaces attacker behavior in logs.

**Start command:**

.. code-block:: bash

   ./scripts/startup.sh basic

----

Level: complete
---------------

Adds active deception that generates noise, expands the attack surface, and makes it harder to distinguish real assets from decoys.

**What changes beyond basic:**

- **Fake Activity Generator** (``fake-activity/``) starts running. It creates realistic SSH sessions using `paramiko`, executing role-appropriate commands (e.g., ``root`` runs ``ps aux``, ``cat /etc/passwd``; ``juan`` runs ``ls -la``, ``df -h``) every 10–30 seconds. These sessions are indexed in Elasticsearch with ``fake_activity: true`` — they look identical to real attacker sessions in logs.
- **Decoy API endpoints** activate in the backend. Endpoints like ``/admin/backup``, ``/internal/config``, ``/internal/secrets``, and ``/dev/database`` respond with convincing fake data and log every access as a security indicator.
- **Hidden HTML links** are injected into the frontend's homepage pointing to the decoy endpoints. They are invisible in the browser but visible to web crawlers and automated scanners.
- **Extra database columns**: ``users`` table gains ``password2`` (alternate password) and ``password_selector`` (single-character flag). An attacker who extracts the schema and finds these will spend time trying to use them, generating additional detectable activity.

**What an attacker sees:**

- SSH activity that looks like other users are active on the system.
- Juicy-looking admin endpoints that log every access.
- Database columns suggesting a more complex authentication scheme.
- More apparent targets, increasing the time spent before reaching real assets.

**Purpose:** Force the attacker to slow down and validate each finding. Every interaction with a decoy generates a high-confidence alert.

**Start command:**

.. code-block:: bash

   ./scripts/startup.sh complete

----

Level: impossible
-----------------

Maximum deception. The attacker is surrounded by convincing but false information at every layer of the stack.

**What changes beyond complete:**

Database layer
^^^^^^^^^^^^^^

The ``init-impossible.sql`` script loads the most extensive deceptive dataset:

- **``system_config`` table** — 22 rows of fake but realistic-looking secrets: Stripe live keys, PayPal client secrets, AWS access/secret keys, JWT secrets, SMTP passwords, Redis/Elasticsearch/Kibana/Grafana/Jenkins/Git tokens, a backup encryption key, and a disaster recovery token. Every exfiltration of this table gives an attacker a long list of credentials to test and pivot on — all false.
- **``api_keys`` table** — 8 rows with realistic API keys for Stripe, PayPal, AWS S3, JWT, email, Redis, Elasticsearch, and Kibana.
- **``ssh_keys`` table** — 5 rows with fake RSA, Ed25519, and ECDSA private keys for production, development, staging, backup, and AWS servers.
- **``file_system`` table** — mimics ``/proc``-style data: fake entries for ``/etc/passwd``, ``/etc/shadow``, ``/home/admin/.ssh/id_rsa``, and ``/var/log/auth.log`` with realistic content previews.
- **``network_info`` table** — maps internal hostnames to IPs and ports, giving an attacker a ready-made internal network map that may not match reality.
- **``exposed_credentials`` view** — a SQL view that joins 20 decoy users with their plaintext passwords, specifically named to attract an attacker scanning for low-hanging fruit.
- **20+ decoy user accounts** — service accounts (``jenkins``, ``git_user``, ``elastic``, ``kibana``, ``logstash``, ``grafana``, ``aws_user``, ``dr_admin``, etc.) each with two password variants (``password`` and ``password2``) and a ``password_selector`` flag. Brute force or SQLi extraction surfaces dozens of credentials to test.

Service banners
^^^^^^^^^^^^^^^

Modified service response headers and SSH banners present misleading version information (e.g., an older, patchable version) to misdirect fingerprinting and exploit selection.

Anti-forensics indicators
^^^^^^^^^^^^^^^^^^^^^^^^^

The environment logs specific patterns that would only appear if an attacker attempts log deletion or evidence tampering (``T1070``). These are detected by Filebeat and generate ``CRITICAL`` severity alerts.

Tampered executables / decoy binaries
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Selected system tools inside containers are replaced or wrapped with decoy versions that log their invocation. When an attacker runs a tool expecting silent output, the execution is recorded as a high-fidelity indicator (``T1036.005``).

**What an attacker sees:**

- Every database table contains a mix of real vulnerabilities and false data.
- Credential extraction yields dozens of API keys, SSH keys, and passwords — all invalid.
- Network maps point to hosts that may not exist or are additional honeypots.
- Even forensic cleanup attempts trigger alerts.

**Purpose:** Maximize attacker confusion and dwell-time. Every action generates signal. By the time the attacker realizes the environment is deceptive, they have fully revealed their tooling, techniques, and objectives in the logs.

**Start command:**

.. code-block:: bash

   ./scripts/startup.sh impossible

----

Attacker Experience by Level
-----------------------------

The table below describes what a skilled attacker observes at each level after running the full 7-phase attack simulation.

.. list-table::
   :header-rows: 1
   :widths: 35 15 15 20 25

   * - Observation
     - none
     - basic
     - complete
     - impossible
   * - SQL injection extracts real users only
     - ✅
     - ✅
     - Mix of real + decoy columns
     - Real + decoy + fake service accounts
   * - SSH brute force hits real service
     - No SSH
     - Honeypot only
     - Honeypot + fake activity noise
     - Honeypot + fake activity noise
   * - Config files reveal credentials
     - ❌
     - ✅ (fictitious)
     - ✅ (fictitious)
     - ✅ (fictitious + API key table)
   * - Decoy API endpoints respond
     - ❌
     - ❌
     - ✅ (logs access)
     - ✅ (logs access)
   * - Database holds API / SSH keys
     - ❌
     - ❌
     - ❌
     - ✅ (all false)
   * - Log deletion detected
     - ❌
     - ❌
     - ❌
     - ✅

----

Choosing a Level for Demos
---------------------------

- **Arsenal / conference demo**: Use ``complete`` — it generates the most visual activity in Kibana without overwhelming first-time audiences.
- **Workshop / hands-on training**: Use ``basic`` first, then switch to ``complete`` to show the impact of each added layer.
- **Advanced red team research**: Use ``impossible`` — it tests whether tooling and techniques survive maximum deception density.
- **Baseline measurement**: Use ``none`` — no noise, pure vulnerability exposure.
