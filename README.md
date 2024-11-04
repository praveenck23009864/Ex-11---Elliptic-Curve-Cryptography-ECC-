# Ex-11 - Elliptic Curve Cryptography (ECC)

## Date:

## Aim:  
To implement the Elliptic Curve Cryptography (ECC) algorithm for secure key generation.

## Algorithm:

1. Define the elliptic curve parameters and base point `G`.
2. Each party (Alice and Bob) selects a private key.
3. Alice computes her public key: `A = d_A X G`, where:
   - `d_A` is Alice's private key,
   - `A` is Alice's public key.
4. Bob computes his public key: `B = d_B X G`, where:
   - `d_B` is Bob's private key,
   - `B` is Bob's public key.
5. Alice and Bob exchange their public keys `A` and `B`.
6. Alice computes the shared secret: `S = d_A X B`.
7. Bob computes the shared secret: `S = d_B X A`.
8. Both Alice and Bob arrive at the same shared secret `S`, which can be used for secure communication.

## Program:
```
#include <iostream>
#include <utility>

struct Point {
    long long x, y;
};

// Elliptic curve parameters
const long long a = 2;    // Curve parameter a
const long long b = 3;    // Curve parameter b
const long long p = 97;   // Prime modulus for finite field (small example)

// Modular exponentiation
long long mod_exp(long long base, long long exp, long long mod) {
    long long result = 1;
    base = base % mod;
    while (exp > 0) {
        if (exp % 2 == 1) {
            result = (result * base) % mod;
        }
        exp = exp >> 1;
        base = (base * base) % mod;
    }
    return result;
}

// Modular inverse using Fermat's Little Theorem
long long mod_inverse(long long x, long long mod) {
    return mod_exp(x, mod - 2, mod);
}

// Point addition on the elliptic curve
Point point_addition(Point P, Point Q) {
    if (P.x == Q.x && P.y == Q.y) { // Point doubling
        long long m = (3 * P.x * P.x + a) * mod_inverse(2 * P.y, p) % p;
        long long x_r = (m * m - 2 * P.x) % p;
        long long y_r = (m * (P.x - x_r) - P.y) % p;
        return {x_r < 0 ? x_r + p : x_r, y_r < 0 ? y_r + p : y_r};
    } else { // General addition
        long long m = (Q.y - P.y) * mod_inverse(Q.x - P.x, p) % p;
        long long x_r = (m * m - P.x - Q.x) % p;
        long long y_r = (m * (P.x - x_r) - P.y) % p;
        return {x_r < 0 ? x_r + p : x_r, y_r < 0 ? y_r + p : y_r};
    }
}

// Scalar multiplication on the elliptic curve
Point scalar_multiplication(Point P, long long k) {
    Point result = P;
    for (long long i = 1; i < k; i++) {
        result = point_addition(result, P);
    }
    return result;
}

int main() {
    // Define the base point G
    Point G = {3, 6};

    // Private keys for Alice and Bob
    long long d_A = 15; // Alice's private key
    long long d_B = 13; // Bob's private key

    // Public keys
    Point A = scalar_multiplication(G, d_A); // Alice's public key
    Point B = scalar_multiplication(G, d_B); // Bob's public key

    // Shared secrets
    Point S_A = scalar_multiplication(B, d_A); // Shared secret calculated by Alice
    Point S_B = scalar_multiplication(A, d_B); // Shared secret calculated by Bob

    std::cout << "Alice's Public Key: (" << A.x << ", " << A.y << ")\n";
    std::cout << "Bob's Public Key: (" << B.x << ", " << B.y << ")\n";
    std::cout << "Shared Secret calculated by Alice: (" << S_A.x << ", " << S_A.y << ")\n";
    std::cout << "Shared Secret calculated by Bob: (" << S_B.x << ", " << S_B.y << ")\n";

    if (S_A.x == S_B.x && S_A.y == S_B.y) {
        std::cout << "The shared secret is successfully established!" << std::endl;
    } else {
        std::cout << "Error: The shared secrets do not match!" << std::endl;
    }

    return 0;
}
```
## Output:

![image](https://github.com/user-attachments/assets/b4d42925-a34c-4239-9ccd-b4b0dd61ddd3)

## Result:

The Elliptic Curve Cryptography (ECC) algorithm is successfully implemented, and both parties (Alice and Bob) arrive at the same shared secret, enabling secure communication.
