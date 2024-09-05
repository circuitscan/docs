CLI Usage
=========

.. _installation:

Installation
------------

To use the Circuitscan CLI, first install it using npm:

.. code-block:: console

    $ npm install -g circuitscan

API Key can be specified any of the following ways in order of precedence:

1. Command line argument `-a` or `--api-key`
2. `CIRCUITSCAN_API_KEY` environment variable
3. `~/.circuitscan` JSON user configuration `{ "apiKey": "xxx" }`

Verify a circuit verifier already deployed on chain
---------------------------------------------------

A circuit verifier is verified on Circuitscan by submitting its original source code along with all the compilation options. On Circuitscan's servers, the source is compiled into its R1CS, the appropriate PTAU file is downloaded, the final ZKey is verified on Groth16 verifiers with a second-phase trusted setup, and finally the Solidity verifier source is compared against what is retrieved for the contract address from the block explorer.

Small differences in the Solidity sources are allowed:

* Adding or removing newlines
* The `errant hardhatc/console.sol include on PLONK verifiers <https://github.com/iden3/snarkjs/pull/464>`_ may be omitted
* Contract name

.. code-block:: console

    Usage: circuitscan verify [options] <mainCircomFile> <chainId> <verifierContractAddress>

    Verify verifier contracts by their circom sources. Can also specify chain by name.

    Options:
      -p, --protocol <protocol>             Specify the protocol: groth16 (default), fflonk, plonk (overrides circomkit.json if available)
      -k, --proving-key <provingKey>        Specify the proving key filename or url (optional, for Groth16 trusted setups). Must be https hosted if >6 MB
      -v, --circom-version <circomVersion>  Specify the Circom version (e.g. "v2.1.8")
      -s, --snarkjs-version <snarkjsVersion>  Specify the SnarkJS version (e.g. "0.7.3")
      -i, --instance <memorySize>           Specify the memory (GB) of compiler instance: 4, 8, 16, 32, 64, 128, 256, 384, 512 (default: 10GB lambda, faster init for small circuits)
      -r, --resume <requestId>              In case of errors during compilation, reattach to a job and attempt a new verification. Overrides all other options.
      -c, --config <configUrl>              Specify a different configuration file (default: https://circuitscan.org/cli.json)
      -a, --api-key <apiKey>                  Specify your API Key as a command line argument
      -h, --help                            display help for command

Compile a circuit and deploy its circuit verifier on chain
----------------------------------------------------------

Alternatively, the CLI can be used to compile and deploy the circuit verifier directly from source, ensuring that the verification happens smoothly.

.. note::

   The DEPLOYER_PRIVATE_KEY environment variable must be set to use the deploy command. An option to use your browser wallet is coming soon...

.. code-block:: console

   Usage: circuitscan deploy [options] <mainCircomFile> <chainId>

    Deploy verifier contract by their circom sources. Can also specify chain by name.

    Options:
      -p, --protocol <protocol>             Specify the protocol: groth16 (default), fflonk, plonk (overrides circomkit.json if available)
      -k, --proving-key <provingKey>        Specify the proving key filename or url (optional, for Groth16 trusted setups). Must be https hosted if >6 MB
      -v, --circom-version <circomVersion>  Specify the Circom version (e.g. "v2.1.8")
      -s, --snarkjs-version <snarkjsVersion>  Specify the SnarkJS version (e.g. "0.7.3")
      -i, --instance <memorySize>           Specify the memory (GB) of compiler instance: 4, 8, 16, 32, 64, 128, 256, 384, 512 (default: 10GB lambda, faster init for small circuits)
      -r, --resume <requestId>              In case of errors during compilation, reattach to a job and attempt a new deploy. Overrides all other options.
      -c, --config <configUrl>              Specify a different configuration file (default: https://circuitscan.org/cli.json)
      -a, --api-key <apiKey>                  Specify your API Key as a command line argument
      -h, --help                            display help for command

Resuming from a build job
-------------------------

Some circuits take a long time to compile and it may not be feasible to have your local machine connected and waiting for the entire duration.

By taking note of the request ID output at the start of a deploy or verify command, you can close out of the process at any time before compilation completes and restart the process later.

In addition to helping with long running jobs or spotty internet connections, this allows verifying the same circuit verifier it accross multiple deployments without waiting for another compilation.

More information
----------------

* `CLI Github Repository <https://github.com/circuitscan/cli>`_

