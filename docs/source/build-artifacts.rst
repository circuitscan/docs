Build Artifact Zip Files
========================

Every circuit verifier on Circuitscan exposes its entire build artifacts directory as a zip file.

Click on the "Download Entire Build" icon (the third icon next to the contract address) to download the zip file.

Circomkit development
---------------------

Included is a pre-configured `Circomkit <https://github.com/erhant/circomkit>`_ project that can be used to:

* generate proofs
* verify proofs
* recompile the circuit
* develop further on the circuit

From inside the extracted directory, run the folowing commands to recompile the circuit from source: (`Circom <https://docs.circom.io/getting-started/installation/>`_ and Node.js must be installed)

.. code-block:: console

    npm install
    npm run compile

See the included readme for a code example to prove or verify the circuit from your Javascript application.

Build Artifacts
---------------

Inside the build directory, you will find:

* proving key/final zkey e.g. ``groth16_pkey.zkey``
* solidity verifier contract e.g. ``groth16_verifier.sol``
* verifier key e.g. ``groth16_vkey.json``
* R1cs representation ``verify_circuit.r1cs``
* Witness in sym format ``verify_circuit.sym``
* WASM proof generator ``verify_circuit_js/verify_circuit.wasm``

Watch the watchmen
^^^^^^^^^^^^^^^^^^

Use the files in the build artifacts directory together with the `snarkjs CLI <https://github.com/iden3/snarkjs/>`_ to double-check that Circuitscan is displaying the correct information.

If you wish to be absolutely certain that the information on Circuitscan is correct, you may recompile the circuit from the source and then verify the final zkey against your new r1cs file.

.. code-block:: console

    npm install
    # Create a new r1cs file from the circom sources
    npm run compile
    # Download the ptau for the circuit's constraint count
    # If a specific PTAU file is linked on the details page on Circuitscan,
    # use that PTAU file instead to verify the zkey.
    npx circomkit ptau mycircuit
    # Adjust these paths for your configuration
    npx snarkjs zkey verify build/mycircuit/mycircuit.r1cs ptau/powersOfTau28_hez_final_08.ptau build/verify_circuit/groth16_pkey.zkey

In-Browser Proof Generator
--------------------------

On each circuit details page, Circuitscan offers an in-browser proof generator that loads the final zkey and WASM proof generator from this build artifacts zip and calculates proofs using your local machine. Your data is never sent to Circuitscan or any third-parties.

The proof generator provides an input template filled with 1 for every possible input signal. Customize the values to fit your witness.

After successful proof generation, the proof is verified against the deployed verifier contract using an RPC call to the blockchain node. A message will appear at the top of the page confirming whether the proof verified on chain or not.

The proof and public signals will be output on screen for your own use.
