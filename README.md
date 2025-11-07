# 64-bit-Block-Cipher-Implementation
Originally completed in February–March 2024 for a personal cryptography project. Uploaded to GitHub in November 2025 for portfolio purposes.

Author: Tristan Nygaard

Language: Java

A custom symmetric encryption algorithm implemented in Java, based on a 64-bit Feistel network design. Supports file encryption and decryption using a 56-bit key.

Features:

64-bit block cipher with 10 Feistel rounds

Substitution (S-box), permutation (P-box), key scheduling, and XOR operations

ECB mode encryption/decryption for text files

File I/O for plaintext, ciphertext, and output generation

Built-in test suite for verifying algorithm correctness

Special instructions: 

When selecting between Encryption/Decryption, type "E" or "D" as the program is case sensitive.
  
When selecting file names to read/write from, name the full file name (readFile.txt should be written as "readFile.txt"). This is also case sensitive.

Sample Inputs:

Do you want to encrypt or decrypt (E/D): E

Filename: input.txt

Secret key: 10101100111101001010011110010100100111011001001110101101

Output file: encrypted.txt
