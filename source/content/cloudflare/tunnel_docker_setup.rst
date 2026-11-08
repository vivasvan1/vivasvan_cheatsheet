.. index:: ! Cloudflare Tunnel with Docker

Cloudflare Tunnel with Docker Services
======================================

Exposing local Docker services securely on custom subdomains via Cloudflare Tunnel (``cloudflared``) without opening ports or configuring dynamic DNS.

Method 1: Cloudflare Tunnel (100% Free & Native)
------------------------------------------------

Since your domain uses Cloudflare DNS and ``cloudflared`` is installed locally on macOS, Cloudflare Tunnels provide:

* Persistent custom subdomains (e.g. ``speede.site``) at zero cost.
* Automatic SSL / HTTPS certificates.
* Direct routing to multiple local ports simultaneously (Shrutam-2 on 8000 and omlx on 48173).
* Automatic reconnection across network switches and system restarts.


1. Clone & Start Docker Services
--------------------------------

Clone the repository and run the Docker services:

.. code-block:: bash

    # Clone the repository
    $ git clone https://github.com/vivasvan1/oriven.git

    # Navigate into directory
    $ cd oriven

    # Start docker services in detached mode
    $ docker compose up -d --build

Verify that containers are up and listening:

.. code-block:: bash

    # Check container status
    $ docker compose ps

    # Verify Shrutam-2 is listening on port 8000
    $ curl -I http://localhost:8000

    # Verify omlx is listening on port 48173
    $ curl -I http://localhost:48173


2. Login & Create Tunnel
------------------------

Authenticate with Cloudflare and generate a named tunnel:

.. code-block:: bash

    # Authenticate with your Cloudflare account (select speede.site in browser)
    $ cloudflared tunnel login

    # Create a named tunnel for your Oriven services
    $ cloudflared tunnel create oriven-tunnel

This creates tunnel credentials at ``~/.cloudflared/<TUNNEL_ID>.json``.


3. Configure Routing
--------------------

Create or edit ``~/.cloudflared/config.yml``:

.. code-block:: yaml

    tunnel: <YOUR_TUNNEL_ID>
    credentials-file: /Users/vivasvan.patel/.cloudflared/<YOUR_TUNNEL_ID>.json

    ingress:
      - hostname: transcript.speede.site
        service: http://localhost:8000
      - hostname: task-processing.speede.site
        service: http://localhost:48173
      - service: http_status:404

Validate your configuration file:

.. code-block:: bash

    $ cloudflared tunnel ingress validate


4. Link Subdomains to Cloudflare DNS
------------------------------------

Automatically add CNAME records in Cloudflare DNS pointing to the tunnel:

.. code-block:: bash

    $ cloudflared tunnel route dns oriven-tunnel transcript.speede.site
    $ cloudflared tunnel route dns oriven-tunnel task-processing.speede.site


5. Run & Test Tunnel
--------------------

Test the tunnel manually from the command line:

.. code-block:: bash

    $ cloudflared tunnel run oriven-tunnel

Verify both endpoints respond over HTTPS:

.. code-block:: bash

    $ curl -I https://transcript.speede.site
    $ curl -I https://task-processing.speede.site


6. Install as a System Service (Daemon)
---------------------------------------

Install and start the tunnel as a persistent macOS system daemon that survives restarts and network drops:

.. code-block:: bash

    $ sudo cloudflared service install
    $ sudo launchctl start com.cloudflare.cloudflared

Check service status or logs:

.. code-block:: bash

    $ sudo launchctl list | grep cloudflare
    $ tail -f /Library/Logs/com.cloudflare.cloudflared.err.log
