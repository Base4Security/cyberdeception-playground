.. _installation:

Installation
============

Requirements
------------

- **Docker** (engine and Compose v2)
- **Resources**: At least 4 GB RAM; recommended 8 GB for full stack + Elasticsearch
- **Ports**: 3000 (frontend), 22 (SSH honeypot), 5601 (Kibana), 9200 (Elasticsearch) — backend and MySQL are internal only
- **Disk**: Sufficient space for Docker images and Elasticsearch data

Supported platforms
-------------------

- **Linux / macOS**: Use ``./scripts/startup.sh <level>`` (bash).
- **Windows**: Use ``.\scripts\startup.bat <level>`` (PowerShell or cmd).

Supported languages / stack
----------------------------

- **Runtime**: Node.js (frontend, backend), Python (SSH honeypot, fake activity, attacker scripts)
- **Databases**: MySQL
- **Containers**: Docker / Docker Compose (all services containerized)

Steps
-----

1. **Clone the repository**

   .. code-block:: bash

      git clone https://github.com/Base4Security/cyberdeception-playground.git
      cd cyberdeception-playground

2. **Start the environment** with a deception level (see :doc:`usage` for parameters).

   .. code-block:: bash

      # Linux / macOS
      ./scripts/startup.sh <level>

      # Windows
      .\scripts\startup.bat <level>

3. **Verify services**

   .. code-block:: bash

      docker compose ps
      curl http://localhost:5601   # Kibana
      curl http://localhost:3000   # Frontend
      docker exec attacker-tools whoami
