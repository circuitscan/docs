CLI Usage
=========

.. toctree::

   usage-circom
   usage-noir

Installation
------------

To use the Circuitscan CLI, first install it using npm:

.. code-block:: console

    $ npm install -g circuitscan

Generate an API key by connecting your browser wallet at the `manage API key <https://circuitscan.org/manage-api-key>`_ page. No transactions are required and this is completely free to use.

API Key can be specified any of the following ways in order of precedence:

1. Command line argument ``-a`` or ``--api-key``
2. ``CIRCUITSCAN_API_KEY`` environment variable
3. ``~/.circuitscan`` JSON user configuration ``{ "apiKey": "xxx" }``

