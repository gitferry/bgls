我是光年实验室高级招聘经理。
我在github上访问了你的开源项目，你的代码超赞。你最近有没有在看工作机会，我们在招软件开发工程师，拉钩和BOSS等招聘网站也发布了相关岗位，有公司和职位的详细信息。
我们公司在杭州，业务主要做流量增长，是很多大型互联网公司的流量顾问。公司弹性工作制，福利齐全，发展潜力大，良好的办公环境和学习氛围。
公司官网是http://www.gnlab.com,公司地址是杭州市西湖区古墩路紫金广场B座，若你感兴趣，欢迎与我联系，
电话是0571-88839161，手机号：18668131388，微信号：echo 'bGhsaGxoMTEyNAo='|base64 -D ,静待佳音。如有打扰，还请见谅，祝生活愉快工作顺利。

# BGLS
Master: [![Build Status](https://travis-ci.org/Project-Arda/bgls.svg?branch=master)](https://travis-ci.org/Project-Arda/bgls)
Develop: [![Build Status](https://travis-ci.org/Project-Arda/bgls.svg?branch=develop)](https://travis-ci.org/Project-Arda/bgls)

Aggregate and Multi Signatures based on BGLS over Alt bn128 and BLS12-381

This library provides no security against side channel attacks. We provide no security guarantees of this implementation.

## Design
The goal of this library is to create an efficient and secure ad hoc aggregate and multi signature scheme. It supports the curves [bls12-381](https://github.com/dis2/bls12) and [alt bn128](https://github.com/ethereum/go-ethereum/tree/master/crypto/bn256). It implements hashing of arbitrary byte data to curve points, the standard BGLS scheme for aggregate signatures, and a custom multi signature scheme. Further documentation for the bgls scheme is contained [here](bgls/README.md)

## Curves
See [here](curves/README.md) for documentation on the supported curves.
## Benchmarks
The following benchmarks are from a 3.80GHz i7-7700HQ CPU with 16GB ram. The aggregate verification is utilizing parallelization for the pairing operations. The multisignature has parellilization for the two involved pairing operations, and parallelization for the pairing checks at the end. Note, all of the benchmarks need to be updated.

For reference, the pairing operation on Altbn128 (the slowest operation involved) takes ~1.9 milliseconds.
```
BenchmarkPairing-8   	    1000	   1958898 ns/op
```
and for Bls12 its:
```
BenchmarkPairGT-8               	    1000	   1539918 ns/op
```

- `Signing` ~.22 milliseconds
- `Signature verification` ~3.1 milliseconds, using two pairings.
- `Multi Signature verification` ~2 milliseconds + ~1.1 microseconds per signer, two pairings + n point additions
- `Aggregate Signature verification` ~.36 milliseconds per signer/message pair, with n+1 pairings run in parallel. (4.45x speedup with 8 cores)

The following benchmarks are done with altbn128, before the product of pairings
abstraction was included. These need to be updated.
```
$ go test github.com/Project-Arda/bgls/bgls/  -v -bench .
BenchmarkKeygen-8                  	    3000	    434484 ns/op
BenchmarkAltBnHashToCurve-8        	   20000	     91947 ns/op
BenchmarkSigning-8                 	   10000	    218670 ns/op
BenchmarkVerification-8            	     500	   3079415 ns/op
BenchmarkMultiVerification64-8     	    1000	   2056798 ns/op
BenchmarkMultiVerification128-8    	    1000	   2140613 ns/op
BenchmarkMultiVerification256-8    	     500	   2334271 ns/op
BenchmarkMultiVerification512-8    	     500	   2617277 ns/op
BenchmarkMultiVerification1024-8   	     500	   3243045 ns/op
BenchmarkMultiVerification2048-8   	     300	   4325183 ns/op
BenchmarkAggregateVerification-8   	    5000	    361270 ns/op
PASS
ok  	github.com/Project-Arda/bgls	31.043s
```
For comparison, the ed25519 implementation in go yields much faster key generation signing and single signature verification. However, at ~145 microseconds per verification, the multi signature verification is actually faster beyond ~26 signatures.
```
$ go test golang.org/x/crypto/ed25519 -bench .
BenchmarkKeyGeneration-8   	   30000	     51878 ns/op
BenchmarkSigning-8         	   30000	     54050 ns/op
BenchmarkVerification-8    	   10000	    145063 ns/op
PASS
ok  	golang.org/x/crypto/ed25519	5.750s
```

## Future work
- Optimize bigint allocations.
- Add hashing to G2
- Integrations with [bgls-on-evm](https://github.com/jlandrews/bgls-on-evm).
- Add tests to show that none of the functions mutate data.
- More complete usage documentation.
- Add buffering for the channels used in parallelization.
- Make upstream libraries implement [product of pairings algorithms](https://eprint.iacr.org/2006/172.pdf)

## References
- Dan Boneh [Methods to prevent the rogue public key attack](https://crypto.stanford.edu/~dabo/pubs/papers/BLSmultisig.html)
- Dan Boneh, Craig Gentry, Ben Lynn, and Hovav Shacham. [Aggregate and verifiably encrypted signatures from bilinear maps](https://www.iacr.org/archive/eurocrypt2003/26560416/26560416.pdf)
- Pierre-Alain Fouque and Mehdi Tibouchi. [Indifferentiable Hashing to
Barreto–Naehrig Curves](http://www.di.ens.fr/~fouque/pub/latincrypt12.pdf)
