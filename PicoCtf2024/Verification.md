# Verification - forensics

## Case

People keep trying to trick my players with imitation flags. I want to make sure they get the real thing! I'm going to provide the SHA-256 hash and a decrypt script to help you know that my flags are legitimate.

Challenge files can be downloaded via: https://artifacts.picoctf.net/c_rhea/21/challenge.zip

## Solution

We are provided a checksum file which contains the following SHA-256 hash: 

> 3ad37ed6c5ab81d31e4c94ae611e0adf2e9e3e6bee55804ebc7f386283e366a4

In order to find our flag file, we must identify a file with a matching hash. SHA-256 hashes can be computed via the command sha256sum:

> sha256sum files/* | grep 3ad37ed6c5ab81d31e4c94ae611e0adf2e9e3e6bee55804ebc7f386283e366a4

The use of /* computes the hash of every file within the folder "files". The output is piped into the 'grep' command which filters exclusively for results containing the given checksum.

