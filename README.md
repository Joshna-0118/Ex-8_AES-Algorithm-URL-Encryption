# Ex-8_AES-Algorithm-URL-Encryption
Implementation of the Advanced Encryption Standard (AES) algorithm in C using OpenSSL for URL encryption and decryption.

## NAME: JOSHNA.M
## REFERENCE NUMBER: 212225230118

# AIM:
To implement the Advanced Encryption Standard (AES) algorithm for encrypting and decrypting a URL using the OpenSSL library in C.
# ALGORITHM:
AES is based on a design principle known as a substitution–permutation. AES does not use a Feistel network like DES, it uses variant of Rijndael. It has a fixed block size of 128 bits, and a key size of 128, 192, or 256 bits. AES operates on a 4 × 4 column-major order array of bytes, termed the state.

# PROGRAM:
```

#include <stdio.h>
#include <string.h>
#include <openssl/evp.h>
#include <openssl/rand.h>

int main() {
    unsigned char key[16] = "0123456789abcdef";
    unsigned char iv[16], encrypted[1024], decrypted[1024];
    char url[500], encoded[1500];
    int len, total, i;

    EVP_CIPHER_CTX *ctx = EVP_CIPHER_CTX_new();

    printf("Enter URL: ");
    fgets(url, sizeof(url), stdin);
    url[strcspn(url, "\n")] = '\0';

    // Generate random IV
    RAND_bytes(iv, sizeof(iv));

    // AES Encryption
    EVP_EncryptInit_ex(ctx, EVP_aes_128_cbc(), NULL, key, iv);
    EVP_EncryptUpdate(ctx, encrypted, &len,
                      (unsigned char *)url, strlen(url));
    total = len;

    EVP_EncryptFinal_ex(ctx, encrypted + total, &len);
    total += len;

    // Convert encrypted data to Base64
    unsigned char data[1040];
    memcpy(data, iv, 16);
    memcpy(data + 16, encrypted, total);

    EVP_EncodeBlock((unsigned char *)encoded, data, total + 16);

    // Make Base64 URL-safe
    for (i = 0; encoded[i]; i++) {
        if (encoded[i] == '+') encoded[i] = '-';
        if (encoded[i] == '/') encoded[i] = '_';
    }

    printf("\nEncrypted URL: %s\n", encoded);

    // Decode Base64
    for (i = 0; encoded[i]; i++) {
        if (encoded[i] == '-') encoded[i] = '+';
        if (encoded[i] == '_') encoded[i] = '/';
    }

    unsigned char input[1500];
    int size = EVP_DecodeBlock(input, (unsigned char *)encoded,
                               strlen(encoded));

    // AES Decryption
    EVP_DecryptInit_ex(ctx, EVP_aes_128_cbc(), NULL, key, input);
    EVP_DecryptUpdate(ctx, decrypted, &len, input + 16, size - 16);
    total = len;

    EVP_DecryptFinal_ex(ctx, decrypted + total, &len);
    total += len;
    decrypted[total] = '\0';

    printf("Decrypted URL: %s\n", decrypted);

    EVP_CIPHER_CTX_free(ctx);
    return 0;
}
```
# OUTPUT:

<img width="1390" height="294" alt="image" src="https://github.com/user-attachments/assets/9e2d9d86-841c-404b-9a5c-68ca9cfead23" />

# RESULT:
Thus, the Advanced Encryption Standard (AES) algorithm was successfully implemented in C using the OpenSSL library. The given URL was encrypted and decrypted successfully.
