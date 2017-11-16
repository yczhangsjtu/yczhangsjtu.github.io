---
title: OpenSSL Elliptic Curve
---

# OpenSSL Elliptic Curve

To use elliptic curve cryptography in OpenSSL, use the header file `openssl/ec.h`. However, note that there are two header files in folder `ec`, one is `ec.h` and another is `ec_lcl.h`.

Usually, developers put a lot of internal functions involving the implementation detail in other header files, and include all of them in the final header, which will be included by the user.

However, in OpenSSL case, although `ec_lcl.h` contains a lot of details of the implementation, the truth is `ec_lcl.h` includes `ec.h` instead of otherwise. Then most of the source files include `ec_lcl.h` instead of `ec.h`. And the users use `ec.h` instead of `ec_lcl.h`.

That means the content of `ec_lcl.h` never appears in the programs developed by OpenSSL users.

So when `gcc` tries to compile some source file linking the `crypto.a` library, it never knows the structure of `EC_KEY`, which in `ec.h` is defined by `typedef struct ec_key_st EC_KEY`, but the structure of `ec_key_st` is defined in `ec_lcl.h`.

This means, you can never declare something as type `EC_KEY`. You can only declare a pointer of type `EC_KEY*`, and the compiler never know what kind of structure this pointer points to.

To handle such pointers, you have to use OpenSSL API. So, although written in C, OpenSSL actually declare all members of `EC_KEY` to be private.

This is one thing to note about OpenSSL. The cases in RSA and other modules are similar.