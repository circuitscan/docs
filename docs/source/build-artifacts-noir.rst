Noir Build Zip
==============

Build Artifacts
---------------

Inside the ``target`` directory, you will find:

* proving key/circuit e.g. ``<circuit_name>.zkey``
* solidity verifier contract e.g. ``contract.sol``
* verifier key e.g. ``vk``

In-Browser Proof Generator
--------------------------

On each circuit details page, Circuitscan offers an in-browser proof generator that loads the circuit from this build artifacts zip and calculates proofs using your local machine. Your data is never sent to Circuitscan or any third-parties.

The proof generator provides an input template filled with 1 for every possible input signal. Customize the values to fit your input.

After successful proof generation, the proof is verified against the deployed verifier contract using an RPC call to the blockchain node. A message will appear at the top of the page confirming whether the proof verified on chain or not.

The proof and public signals will be output on screen for your own use.

.. note::

   The proof will only validate successfully on chain in the proof generator if the deployed contract is an unmodified contract from the Barretenberg backend output.

   If the contract is not a simple verifier output from the template, you may have to copy the proof data displayed on screen into a different function exposed by the contract.

.. note::

   Proof generation for Noir circuits is still under construction!

   As a workaround, download the build zip and generate proofs on your local machine using the `Noir command line tools <https://noir-lang.org/docs/getting_started/hello_noir/#proving-backend>`_.

