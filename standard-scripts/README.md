# Standard Bender scripts 

There are several preconfigured [`bender` scripts](../bender-script/README.md) that helps to solve frequent problems. The list includes:
 - `dst-dump` :  the script that allows to _dump the content sumamry_ for the certain (x,u,...)DST-file. 
 - `get-dbtags` : the script that helps  to get `DDDB/CONDB`-tags from certain data file. It coudl be useful for [processing of simulated samples](../simulation/README.md)
 - `get-metainfo` : the script that help  to a lot of available meta-information (including `DDDB/CONDB`-tags) from certain data file and corresponisng entry in `RunDB`. It could be useful for [processing of simulated samples](../simulation/README.md) of other purposes
 - `trg-check` : the  script that  helps to get [the list of the relevant](../advanced-bender/tistos.md) `L0TIS`, `L0TOS`, `Hlt1TIS`, `Hlt1TOS`, `Hlt2TIS` and `Hlt2TOS` trigger lines fo the given selection
 - `no-mc-decays` : helper script that often helps to understand why not all MC-truth  decays are [selected](../simulation/mctruth.md)  using certain [_decay descriptor_](https://twiki.cern.ch/twiki/bin/view/LHCb/FAQ/LoKiNewDecayFinders). It allows to tune the  decay descriptor properly to accept the missing entries.   
 
All there scripts are properly documented and one can invoke them using `-h` option.
