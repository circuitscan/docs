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

Verify a multi-verifier already deployed on chain
-------------------------------------------------

Circuitscan also supports combining multiple Groth16 verifiers into one verification contract.

This is inspired by `Semaphore V4 <https://semaphore.pse.dev/>`_ and is supported using the `snarkjs-groth16-multi-verifier NPM package <https://github.com/circuitscan/snarkjs-groth16-multi-verifier/>`_.

All of the individual verifiers must be verified already on Circuitscan, then you must craft a JSON file containing all of the verifiers in order of their inclusion.

.. code-block:: console

    Usage: circuitscan verify:circom-multi [options] <jsonFile>

    Verify a Groth16 multi-verifier using a JSON specification. See docs website for details.

    Options:
      -c, --config <configUrl>  Specify a different configuration file (default: https://circuitscan.org/cli.json)
      -h, --help                display help for command

The following example JSON describes Semaphore V4 deployed on Sepolia:

.. code-block:: console

    {
      "verifiers": [
        { "chainId": 17000, "address": "0xf24a641276ca49e9984124ddf52df4b0d40e63a3" },
        { "chainId": 17000, "address": "0x467d5a506f0dcfbffaa403656ed1cc1477d657eb" },
        { "chainId": 17000, "address": "0x3e950933555243561f28da3c6a0a515639fe7026" },
        { "chainId": 17000, "address": "0xc174d9b265bf99c5aef393c44a18c9703bef1fa1" },
        { "chainId": 17000, "address": "0x7e5e5cbf5533a22ba58b1c7d7a0ad015c2278eac" },
        { "chainId": 17000, "address": "0x8a8817162b13f1856b6ba4d52abade5428b262f0" },
        { "chainId": 17000, "address": "0xebc1535fe0816c8ee6dbd2474b0ad51934bbe030" },
        { "chainId": 17000, "address": "0xa9bced3e075025dd3751c6cef5e349fbb0278acb" },
        { "chainId": 17000, "address": "0x39374372f3ca97b800e47f747c2fb26440609d13" },
        { "chainId": 17000, "address": "0xfdcfd9f5fc0be0e5f93710bd3d53b6a32fe1f580" },
        { "chainId": 17000, "address": "0x7902dadca10ddda38b768b68a6082f152081d5b3" },
        { "chainId": 17000, "address": "0x56e23ddc0ab53aea6447e8f08eafa9607f483f53" },
        { "chainId": 17000, "address": "0x94e1698410614dab6fd34a14ed06d6b6b2cdefd1" },
        { "chainId": 17000, "address": "0xfc05984d8aa48f4c334ee5eac92f408becf4a867" },
        { "chainId": 17000, "address": "0x9e0f76dd17518b1b4cbae2472bf3d549e469034c" },
        { "chainId": 17000, "address": "0x1fef2d460156d914bf5fb1569d9678979cd42a82" },
        { "chainId": 17000, "address": "0x26f56a11ac9bd2f4a243b03e8875f5b797b07af8" },
        { "chainId": 17000, "address": "0x72feeb885599c74fe041c582c3da3e14214cf106" },
        { "chainId": 17000, "address": "0x3c2ca19e80428f7e19808859483a08cf07476722" },
        { "chainId": 17000, "address": "0x445e9b1b1f0feafad68608d1def398bad3e69018" },
        { "chainId": 17000, "address": "0x3df122cdfcced4b07990f38a2e2cc992a942af31" },
        { "chainId": 17000, "address": "0xdccf1079fa92acd45ab3e30637aa84c4e36ea21e" },
        { "chainId": 17000, "address": "0x5dccc38aa2752ac3e09ead06d51285736b3c1096" },
        { "chainId": 17000, "address": "0xce3f1fd94ab9760b24cf06bf128b86b8926f7b74" },
        { "chainId": 17000, "address": "0xb75859fe64e04bdabe93eed006f925a1089694f1" },
        { "chainId": 17000, "address": "0xad8ff16a45f41151415a0680d0a26fd856402bce" },
        { "chainId": 17000, "address": "0xa23905de7bafac05d3e7028c6d87538445da8b43" },
        { "chainId": 17000, "address": "0xe0b9a7bd7e0746791874d7535afb9271b03e259e" },
        { "chainId": 17000, "address": "0x990f37c12e2138aaefc05089b50459c870739825" },
        { "chainId": 17000, "address": "0xbd0feed838293123b27d329dca5e1610698afd4c" },
        { "chainId": 17000, "address": "0xe516cc1ce72ae1124c277b92755bdceebe745f75" },
        { "chainId": 17000, "address": "0x25cd28fca2474604e2bc1bbc835071de739b6bcf" }
      ],
      "offset": 1,
      "deployed": {
        "chainId": 11155111,
        "address": "0xe538f9DeeE04A397decb1E7dc5D16fD6f123c043"
      },
      "modifier": "semaphorev4"
    }

``verifiers``
^^^^^^^^^^^^^

*Required* An array of at least two verifiers already verified on Circuitscan

``offset``
^^^^^^^^^^

*Required* The starting index of the first verifier for the new argument added to the the contract's ``verifyProof()`` function

``deployed``
^^^^^^^^^^^^

*Required* The address and chain of the deployed multi-verifier contract

``modifier``
^^^^^^^^^^^^

*Optional* To account for greater variation in deployed multi-verifiers, Circuitscan supports additional modifications to the Solidity source code.

Available modifiers: ``semaphorev4``

Submit new modifiers as PRs to this directory: `circuitscan/server/modifiers <https://github.com/circuitscan/circuitscan/tree/main/server/modifiers>`_

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

