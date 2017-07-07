# ZCash miners

## Silent Army

* https://github.com/mbevand/silentarmy
* Need AMD driver, AMD SDK, OpenCL
* Support directly computing Equihash `sa-solver`
* Support stratum protocol through python script `silentarmy`,  which invokes many `sa-solver` instances silently.

## zogminer

* https://github.com/nginnever/zogminer
* Originally support solo mining, but now the solo part moved to the `nginnever` clone of zcash (which is way behind the zcash master branch). In fact, it contains a lot of code of zcash, but the last commit is 8 months ago, so I guess the code already consists the entire nginnever clone of zcash.  Then don't have to worry about zcash, this project itself contains everything. Just build it like zcash.
* Test miner: `zcash-miner -G`
* Stratum: `zcash-miner -G -stratum="stratum+tcp://<pool-address>:<port>" -user=<zaddress> -password=x `
* Solo miner: `zcashd` with proper settings of `zcash.conf` (or command-line parameters). In particular, set `GPU=1` in `zcash.conf` and `-allgpu` in command-line. Only works with the nginnever branch of zcash.

## TODO

* It seems that zogminer is the choice, since DWC is also a bit behind the zcash master branch.
* First of all, forget about DWC, and try to make everything work on zcash. In particular, work on the so called nginnever branch of zcash. Consider storing everything in the 1TB disk, in case that something goes wrong and Ubuntu has to be reinstalled. The network is bottleneck so don't worry about the disk speed.

  1. Download and install zogminer. Install OpenCL development environment as required.
  2. Run `zcash-miner -G` with and without stratum to see it works.
  3. Try solo mining with the not-so-efficient card in the computer.
  4. Try installing an AMD card and make everything still work.
* Compare the `zogminer` branches of [DWC](https://github.com/deepwebcash/deepwebcash/tree/zogminer) with that of nginnever zcash. If the difference is not apparent, and the parameters have been properly modified for DWC, then do everything similarly for DWC.
* If got enough time, read the code! 


