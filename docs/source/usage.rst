CLI Usage
=========

.. _installation:

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

Verify a circuit verifier already deployed on chain
---------------------------------------------------

A circuit verifier is verified on Circuitscan by submitting its original source code along with all the compilation options. On Circuitscan's servers, the source is compiled into its R1CS, the appropriate PTAU file is downloaded, the final ZKey is verified on Groth16 verifiers with a second-phase trusted setup, and finally the Solidity verifier source is compared against what is retrieved for the contract address from the block explorer.

Small differences in the Solidity sources are allowed:

* The `errant hardhatc/console.sol include on PLONK verifiers <https://github.com/iden3/snarkjs/pull/464>`_ may be omitted
* Contract name
* ``pragma solidity`` version differences
* Comment differences
* Whitespace differences
* *Coming Soon* Multi-verifiers a la Semaphore V4

.. code-block:: console

    Usage: circuitscan verify:circom [options] <mainCircomFile> <chainId> <verifierContractAddress>

    Verify verifier contracts by their circom sources. Can also specify chain by name.

    Options:
      -p, --protocol <protocol>             Specify the protocol: groth16 (default), fflonk, plonk (overrides circomkit.json if available)
      -k, --proving-key <provingKey>        Specify the proving key filename or url (optional, for Groth16 trusted setups). Must be https hosted if >6 MB
      -t, --ptau <ptauSize>                   Force a specific Powers of Tau size (8-28 or url to download specific file)
      -v, --circom-version <circomVersion>  Specify the Circom version (e.g. "v2.1.8")
      -s, --snarkjs-version <snarkjsVersion>  Specify the SnarkJS version (e.g. "0.7.3")
      -i, --instance <memorySize>           Specify the memory (GB) of compiler instance: 4, 8, 16, 32, 64, 128, 256, 384, 512 (default: 4 for smallest circuits)
      -r, --resume <requestId>              In case of errors during compilation, reattach to a job and attempt a new verification. Overrides all other options.
      -c, --config <configUrl>              Specify a different configuration file (default: https://circuitscan.org/cli.json)
      -a, --api-key <apiKey>                  Specify your API Key as a command line argument
      -h, --help                            display help for command

Compile a circuit and deploy its circuit verifier on chain
----------------------------------------------------------

Alternatively, the CLI can be used to compile and deploy the circuit verifier directly from source, ensuring that the verification happens smoothly.

.. note::

   The ``DEPLOYER_PRIVATE_KEY`` environment variable and ``chainId`` argument must be set to use the deploy command unless the ``-b`` or ``--browser-wallet`` argument is used.

.. code-block:: console

   Usage: circuitscan deploy:circom [options] <mainCircomFile> [chainId]

    Deploy verifier contract by their circom sources. Can also specify chain by name.

    Options:
      -p, --protocol <protocol>             Specify the protocol: groth16 (default), fflonk, plonk (overrides circomkit.json if available)
      -k, --proving-key <provingKey>        Specify the proving key filename or url (optional, for Groth16 trusted setups). Must be https hosted if >6 MB
      -t, --ptau <ptauSize>                   Force a specific Powers of Tau size (8-28 or url to download specific file)
      -v, --circom-version <circomVersion>  Specify the Circom version (e.g. "v2.1.8")
      -s, --snarkjs-version <snarkjsVersion>  Specify the SnarkJS version (e.g. "0.7.3")
      -i, --instance <memorySize>           Specify the memory (GB) of compiler instance: 4, 8, 16, 32, 64, 128, 256, 384, 512 (default: 4 for smallest circuits)
      -r, --resume <requestId>              In case of errors during compilation, reattach to a job and attempt a new deploy. Overrides all other options.
      -c, --config <configUrl>              Specify a different configuration file (default: https://circuitscan.org/cli.json)
      -a, --api-key <apiKey>                  Specify your API Key as a command line argument
      -b, --browser-wallet                    Send transaction in browser instead of by passing private key env var (overrides chainId argument)
      -h, --help                            display help for command

Command Line Arguments
----------------------

``-p``, ``--protocol``
^^^^^^^^^^^^^^^^^^^^^^

Specify the protocol of the verifier. If ommitted, ``groth16`` is used.

Allowed values:

* ``groth16`` (Default)
* ``fflonk``
* ``plonk``

This value will be retrieved from ``circomkit.json`` if it exists in the current directory or a parent directory.

``-k``, ``--proving-key``
^^^^^^^^^^^^^^^^^^^^^^^^^

Specify the final ZKey proving key for groth16 verifiers.

For proving keys under 6 MB, you may pass the filename and it will be uploaded as part of your request but this is not recommended since it does not reveal how each contribution was made.

    "Without attestations, you can just replace all the contributions with your own, fully knowing the toxic waste, allowing you to create fake proofs"

    -- `@kobigurk <https://x.com/kobigurk/status/1782502989850394950>`_

Therefore, it is recommended to use a final ZKey HTTPS URL from the `PSE P0tion DefinitelySetup website <https://ceremony.pse.dev>`_ to display verified Groth16 Trusted Setup status on the circuit verifier details page.

If you deploy a groth16 circuit verifier using this CLI but do not specify a proving key, Circuitscan will generate a second-phase setup with a random 32 bytes of entropy. Although this value is discarded and is theoretically secure, P0tion provides greater assurance of security because it does not rely on trusting Circuitscan's infrastructure.

``-t``, ``--ptau``
^^^^^^^^^^^^^^^^^

If omitted, Circuitscan will use the smallest Powers of Tau file from the `Polygon zkEVM/hermez ceremony <https://github.com/iden3/snarkjs#7-prepare-phase-2>`_ that fits the circuit based on the number of constraints.

If passing a number between 8 and 28, the specific Ptau file from the hermez ceremony will be downloaded.

Otherwise, pass an https url for the Ptau file you would like to use.

Another commonly used Ptau ceremony is the `PSE P0tion PPoT Trusted Setup Ceremony <https://github.com/privacy-scaling-explorations/p0tion/blob/dev/packages/actions/src/helpers/constants.ts#L80>`_. Pass one of these urls if using P0tion for the second-phase trusted setup.

``-v``, ``--circom-version``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pass the version of the Circom compiler to use.

Allowed values:

* ``v2.1.9``
* ``v2.1.8`` (Default)
* ``v2.1.7``
* ``v2.1.6``
* ``v2.1.5``
* ``v2.1.4``
* ``v2.1.3``
* ``v2.1.2``
* ``v2.1.1``
* ``v2.1.0``
* ``v2.0.9``
* ``v2.0.8``

``-s``, ``--snarkjs-version``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pass the version of SnarkJS to use.

Allowed values:

* ``0.7.4`` (Default)
* ``0.7.3``
* ``0.7.2``
* ``0.7.1``
* ``0.7.0``
* ``0.6.11``

``-i``, ``--instance``
^^^^^^^^^^^^^^^^^^^^^^

Compilations are performed on a cloud machine with the specified number of GB of memory.

Please use the smallest value necessary to compile your circuits. Abuse of large instances will result in your account being banned.

Allowed values: 4, 8, 16, 32, 64, 128, 256, 384, 512

If ommitted, the smallest instance size is used: 4 GB.

``-r``, ``--resume``
^^^^^^^^^^^^^^^^^^^^

Some circuits take a long time to compile and it may not be feasible to have your local machine connected and waiting for the entire duration.

By taking note of the request ID output at the start of a deploy or verify command, you can close out of the process at any time before compilation completes and restart the process later.

In addition to helping with long running jobs or spotty internet connections, this allows verifying the same circuit verifier it accross multiple deployments without waiting for another compilation.

This argument may also be used to re-use a compilation output if the wrong chain/contract address is passed without waiting to compile again.

Request IDs are stored in ``~/.circuitscan-history`` for easy recovery from errors.

``-c``, ``--config``
^^^^^^^^^^^^^^^^^^^^

This argument is only used when connecting your CLI to a different (e.g. self-hosted) instance of Circuitscan.

There is no reason to use this argument when verifying or deploying circuits to circuitscan.org.

``-a``, ``--api-key``
^^^^^^^^^^^^^^^^^^^^^

Overrides API key settings from either the ``CIRCUITSCAN_API_KEY`` environment variable, or the ``~/.circuitscan`` JSON user configuration.

``-b``, ``--browser-wallet``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For the ``deploy:circom`` command, this argument may be used instead of specifying the ``DEPLOYER_PRIVATE_KEY`` environment variable.

A link will be provided after the circuit compiles where you will be able to connect your wallet and deploy the verifier contract.

After your contract is deployed, the verification process will continue in your terminal.

This is a recommended option for desktop use of the Circuitscan CLI. If using in a CI/CD environment, the environment variable provides headless operation.

Additional Configuration
------------------------

A few more circuit configuration options are available if passed using a ``circomkit.json`` file.

Learn more about `Circomkit <https://github.com/erhant/circomkit>`_...

``optimization``
^^^^^^^^^^^^^^^^

Set the Circom compiler optimization level. (Default: 2)

``include``
^^^^^^^^^^^

Specify an array of strings denoting other directories to search for included files.

``prime``
^^^^^^^^^

Specify a prime value other than the default ``bn128``.

Other primes require specifying a PTAU file that matches that prime.

``protocol``
^^^^^^^^^^^^

The protocol may also be specified here instead of by CLI argument.

Verification Errors
-------------------

``invalid_diff``
^^^^^^^^^^^^^^^^

The generated Solidity verifier contract does not match the source code retrieved from the block explorer

Find the generated source at ``https://circuitscan-artifacts.s3.us-west-2.amazonaws.com/build/<build-name-adjective-animal>/verifier.sol``

``contract_not_verified``
^^^^^^^^^^^^^^^^^^^^^^^^^

The contract at this address on the specified is not verified on Sourcify/Blockscout/Etherscan.

Please submit the source to `Sourcify.dev <https://sourcify.dev>`_

``invalid_finalZkey``
^^^^^^^^^^^^^^^^^^^^^^

The final zkey/proving key you specified does not match the r1cs/ptau.

More information
----------------

* `CLI Github Repository <https://github.com/circuitscan/cli>`_

