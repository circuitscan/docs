Circuitscan documentation
=========================

Circuitscan is a circuit verifier explorer. Like verified contracts, a verified circuit verifier allows users to interact with applications directly if the frontend becomes unusable as well as ensures that the deployed source code matches the expected source code. Developers use a CLI to submit their circom sources to the verification server just like how Hardhat and Foundry submit deployed contracts to Etherscan. The frontend catalogs all verified submissions with source code and proving keys available for usage.

Circuit verifier Solidity contracts verified on the block explorer are opaque to the viewer. There's no way to quickly check that they match the source code on Github. When users can see this match, they can be sure that there's no backdoors inserted clandestinely.

Verified contracts on the block explorer also come into play when a dapp's frontend becomes unusable. For a zk app with a missing frontend, Circuitscan provides a tool for generating proofs so that users never have to worry about being locked out.

Check out the :doc:`usage` section for further information about submitting your circuit verifiers, including
how to :ref:`installation` the CLI.

.. note::

   This project is under active development.

Contents
--------

.. toctree::

   usage
   api
