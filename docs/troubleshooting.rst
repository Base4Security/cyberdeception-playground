.. _troubleshooting:

Troubleshooting
===============

Debug logs
----------

View logs from all services:

.. code-block:: bash

   docker compose logs -f

View logs from a specific service:

.. code-block:: bash

   docker compose logs -f ssh-honeypot
   docker compose logs -f fake-activity
   docker compose logs -f filebeat

Common issues
-------------

- **Ports in use**: Ensure 3000, 22, 5601, and 9200 are free (or adjust port mappings in ``docker-compose.yml``).
- **Out of memory**: Elasticsearch can be heavy; ensure at least 4 GB RAM (8 GB recommended).
- **Startup order**: Services depend on MySQL and backend; if something fails to start, run ``docker compose ps`` and check logs for the failing service.
