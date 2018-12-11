---
title: Google Test Brief Introduction
---

# Google Test Brief Introduction

Google Test is a test suite developed by Google.

## Installation (for ubuntu):

```bash
$ sudo apt install libgtest-dev cmake
$ cd /usr/src/gtest
$ sudo cmake .
$ sudo make
$ sudo cp *.a /usr/lib
```

## Example code

```c++
#include <gtest/gtest.h>

TEST(MathTest, TwoPlusTwoEqualsFour) {
  EXPECT_EQ(2+2,4);
}

int main(int argc, char *argv[]) {
  ::testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}
```

Compile by `g++` with parameters

```bash
-lgtest -lgtest_main
```

The resulting program could run independently. 