# 抗量子签名算法Survey

## Hash-based Signature

- [Merkle]提出了第一个基于哈希的签名方案。
- [XMSS]是目前 (2014) 效率最高的方案。
- [XMSS MT]是对[XMSS]的改进，更快的密钥生成，每个公私钥对能够用于更多签名。
- [Internet-Draft 2014]签名太大，运行时间长，一对公私钥对能产生的签名数量有限。它也涵盖了最近提出来的[SPHINCS]所需的最基本的构造工具。

- [XMSS]和HSS类似，签名大小稍小，安全性等价，产生签名的数量也相同。
- 使用SHA256做Hash算法时，HSS约比[XMSS]快四倍，原因是Hash运算占据主要运算代价，而XMSS需要计算四次Hash的地方，HSS只需计算一次，而且HSS的方案更简单。
- [SPHINCS]是纯正的无状态基于哈希的数字签名，但签名大小和时间消耗比HSS显著更高。

[Merkle] Ralph Merkle. A certified digital signature. In Gilles Brassard, editor, Crypto’89, volume 435 of Lecture Notes in Computer Science, pages 218–238. Springer Berlin Heidelberg, 1990.

[XMSS] Johannes Buchmann, Erik Dahmen, and Andreas Hülsing. XMSS — A Practical Forward Secure Signature Scheme Based on Minimal Security Assumptions. In Bo-Yin Yang, editor, Post-Quantum Cryptography 2011, volume 7071 of Lecture Notes in Computer Science, pages 117–129. Springer Berlin / Heidelberg, 2011.

[XMSS MT] Andreas Hülsing, Lea Rausch, and Johannes Buchmann. Optimal parameters for XMSS MT. In Alfredo Cuzzocrea, Christian Kittl, Dimitris E. Simos, Edgar Weippl, and Lida Xu, editors, Security Engineering and Intelligence Informatics, volume 8128 of Lecture Notes in Computer Science, pages 194–208. Springer Berlin Heidelberg, 2013.

[SPHINCS] Daniel J. Bernstein, Daira Hopwood, Andreas Hülsing, Tanja Lange, Ruben Niederhagen, Louiza Papachristodoulou,Peter Schwabe, and Zooko Wilcox O’Hearn. Sphincs: practical stateless hash-based signatures. Cryptology ePrint Archive, Report 2014/795, 2014. [http://eprint.iacr.org/.](http://eprint.iacr.org/) 

- SPHINCS有41KB的签名大小，1KB的公钥，1KB的私钥，在量子计算机存在的前提下能够提供128比特的安全性。
- 基于格的签名相当快，签名大小也相当小，但安全性非常模糊。
- Multivariate-quadratic签名也一样，虽然很快而且很小，但安全性没有保证。
- Code-based签名的签名大小足够小，也有一定的安全理论，但密钥有几兆大小。

[New Standard] Hash-based Signatures: An Outline for a New Standard

[Internet-Draft 2017] David McGrew and Michael Curcio. Hash-Based Signatures. [https://datatracker.ietf.org/doc/active/.](https://datatracker.ietf.org/doc/active/) 

## Lattice-based Signature

[GLIP] T. Güneysu, V. Lyubashevsky, and T. Pöppelmann. Practical lattice-based cryptography: A signature scheme for embedded systems. In E. Prouff and P. Schaumont, editors, Cryptographic Hardware and Embedded Systems – CHES 2012, volume 7428 of Lecture Notes in Computer Science, pages 530–547. Springer, 2012.

[BLISS] L. Ducas, A. Durmus, T. Lepoint, and V. Lyubashevsky. Lattice signatures and bimodal gaussians. In R. Canetti and J. A. Garay, editors, CRYPTO 2013, Part I, volume 8042 of LNCS, pages 40–56, Santa Barbara, CA, USA, Aug. 18–22, 2013. Springer, Heidelberg, Germany.

[RING TESLA] An Efficient Lattice-Based Signature Scheme with Provably Secure Instantiation