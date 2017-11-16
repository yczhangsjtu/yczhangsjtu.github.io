---
title: SRI-CSL-Bliss代码使用样例
---

# SRI-CSL-Bliss代码使用样例

## 使用样例

```C
/* demo.c */
#include <stdio.h>
#include <inttypes.h>
#include <string.h>
#include "bliss_b_errors.h"
#include "bliss_b_keys.h"
#include "bliss_b_signatures.h"
#include "entropy.h"

int main() {
    static uint8_t seed[SHA3_512_DIGEST_LENGTH] = {
        0, 1, 2, 3, 4, 5, 6, 7,
        0, 1, 2, 3, 4, 5, 6, 7,
        0, 1, 2, 3, 4, 5, 6, 7,
        0, 1, 2, 3, 4, 5, 6, 7,
        0, 1, 2, 3, 4, 5, 6, 7,
        0, 1, 2, 3, 4, 5, 6, 7,
        0, 1, 2, 3, 4, 5, 6, 7,
        0, 1, 2, 3, 4, 5, 6, 7
    };
    static entropy_t entropy;
    static bliss_private_key_t private_key;
    static bliss_public_key_t public_key;
    static bliss_signature_t signature;
    
    int32_t type = 1; /* BLISS-1 (alternatives 2,3,4) */
    
    char * text = "Message to sign";
    uint8_t *msg = (uint8_t*) text;
    size_t msg_size = strlen(text)+1;
    entropy_init(&entropy, seed);
    
    if(BLISS_B_NO_ERROR != bliss_b_private_key_gen(&private_key, type, &entropy))
        goto error;
        
    if(BLISS_B_NO_ERROR != bliss_b_public_key_extract(&public_key, &private_key))
        goto error;
        
    if(BLISS_B_NO_ERROR != bliss_b_sign(&signature, &private_key, msg, msg_size, &entropy))
        goto error;
    
    if(BLISS_B_NO_ERROR != bliss_b_verify(&signature, &public_key, msg, msg_size))
        goto error;
    
    fprintf(stderr,"Verified!\n");
        
    bliss_b_private_key_delete(&private_key);
    bliss_b_public_key_delete(&public_key);
    bliss_signature_delete(&signature);
    return 0;
error:
    fprintf(stderr,"Error!\n");
    return -1;
}
```

从github下载源代码（https://github.com/SRI-CSL/Bliss）后直接`make`编译。编译完成后lib文件夹下会生成libbliss.so动态链接库。
将以上代码保存编译：

```shell
$ gcc demo.c -I/path/to/Bliss/include -L/path/to/Bliss/lib -lbliss -o demo
$ LD_LIBRARY_PATH=/path/to/Bliss/lib ./demo
```
