.. _architecture:

Architecture
============

Overview
--------

The lab is organized in networks: External (attacker container), DMZ (frontend), Server (backend, SSH honeypot, fake activity), Database (MySQL), and Monitor (Filebeat, Elasticsearch, Kibana).

Network diagram
---------------

.. code-block:: text

   ┌────────────────────────────────────────────────────────────────────────────────┐
   │                            "EXTERNAL" NETWORK                                    │
   │  ┌─────────────────────────────────────────────────────────────────────────┐   │
   │  │                        Attacker Container                               │   │
   │  │  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────────┐  │   │
   │  │  │  Scripting      │    │   Manual        │    │   Recon Tools       │  │   │
   │  │  │  Attacks        │    │   Testing       │    │   (nmap, etc.)      │  │   │
   │  │  │   Port 5000     │    │   (SSH/Web)     │    │                     │  │   │
   │  │  └─────────────────┘    └─────────────────┘    └─────────────────────┘  │   │
   │  └─────────────────────────────────────────────────────────────────────────┘   │
   └────────────────────────────────────────────────────────────────────────────────┘
                                       │
                                       ▼
   ┌─────────────────────────────────────────────────────────────────────────────────┐
   │                                DMZ NETWORK                                        │
   │                   ┌───────────────────────────────────┐                           │
   │                   │             Frontend                │                           │
   │                   │          Employee Portal           │  Port 3000               │
   │                   └─────────────────────┬──────────────┘                           │
   │                                         │ Backend Communication                    │
   │  ┌─────────────────────────────────────▼──────────────────────────────────────┐   │
   │  │                           SERVER NETWORK                                    │   │
   │  │  ┌───────────────┐    ┌──────────────┐    ┌─────────────────────────┐      │   │
   │  │  │    Backend    │    │ SSH Honeypot │    │   Fake Activity         │      │   │
   │  │  │ Financial API │    │  Port 22     │◄───│   Generator             │      │   │
   │  │  │   Port 3001   │    │              │    │                         │      │   │
   │  │  └───────┬───────┘    └──────────────┘    └─────────────────────────┘      │   │
   │  │          │ Database Access                                                  │   │
   │  │  ┌───────▼───────────────────────────────────────────────────────────────┐  │   │
   │  │  │                     DATABASE NETWORK  │  MySQL Port 3306              │  │   │
   │  │  └───────────────────────────────────────────────────────────────────────┘  │   │
   │  │  ┌───────────────────────────────────────────────────────────────────────┐  │   │
   │  │  │ MONITOR NETWORK  │  Filebeat ──► Elasticsearch (9200) ──► Kibana (5601)│  │   │
   │  │  └───────────────────────────────────────────────────────────────────────┘  │   │
   │  └─────────────────────────────────────────────────────────────────────────────┘   │
   └─────────────────────────────────────────────────────────────────────────────────┘

Technology stack
----------------

- **Frontend**: Andesfinance employee portal (Node.js/Express)
- **Backend**: Andesfinance financial API
- **MySQL**: Financial database with sensitive data
- **SSH Honeypot**: Custom SSH honeypot
- **Fake Activity**: Fake activity generator
- **Elastic Stack**: Filebeat + Elasticsearch + Kibana
- **Docker Compose**: Container orchestration

Security architecture
----------------------

- **Frontend**: Accessible from localhost (port 3000) — controlled entry point
- **Backend**: Only internally accessible — protected from external access
- **MySQL**: Only internally accessible — isolated database
- **SSH Honeypot**: Only accessible internally from server network
- **Elastic Stack**: Accessible from localhost (port 5601) — monitoring entry point

Project structure
-----------------

::

   /cyberdeception-playground/
   ├── docker-compose.yml          # Service orchestration
   ├── frontend/                   # Vulnerable web application
   ├── backend/                    # Vulnerable backend API
   ├── mysql/                      # MySQL database & init scripts per level
   ├── ssh-honeypot/               # SSH Honeypot
   ├── fake-activity/              # Fake activity generator
   ├── attacker/                   # Attacker container & attack_scripts
   ├── filebeat/                   # Log collection agent
   ├── dashboards/                 # Kibana dashboards
   ├── logs/                       # System logs
   ├── scripts/                    # startup.sh, startup.bat, shutdown.bat
   ├── LICENSE
   ├── README.md
   └── README_ES.md
