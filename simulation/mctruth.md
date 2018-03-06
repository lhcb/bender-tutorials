# Access MC-truth information in Bender


As it was mentioned [above](simulation/README.md),  the processing of simulation data in Bender is rather simple, one just needs to inherit the algorithm from base class `AlgoMC`, this class can be imported from  the `Bender.MainMC` module.
```python
from Bender.MainMC import *  # it imports also the whole content of Bender.Main module 
class MyAlg(AlgoMC) : ...
```   
And the corresponding wrapper for `Selection`-framework is `BenderMCSelection`

## _Get MC-truth data_ 

To access to MC-truth data one uses the method `mcselect`, that is very similar to the method `select` discussed [above](../getting-started/firstalgorithms.md)
```python
mcB = self.mcselect ( 'mcB' , '[Beauty ==>  J/psi(1S)  K+  K-]CC' ) 
mcK = self.mcselect ( 'mcK' , '[Beauty ==>  J/psi(1S) ^K+ ^K-]CC' ) ## note marked components 
mcb = self.mcselect ( 'mcb' ,  BEAUTY ) ##  any beauty hadron 
mc0 = self.mcselect ( 'mc0' , 'B0' ==  MCABSID ) ##  B0 or B~0
```
Again, as selection criteria one can use the [_decay descriptors_](https://twiki.cern.ch/twiki/bin/view/LHCb/FAQ/LoKiNewDecayFinders) or _LoKi MC-functors_.
```python
dv   = DaVinci ( Simulation = True , ...  ) 
bsel = BenderMCSelection ( 'MCtruth' , [] )  ## <--- HERE! 
daVinci.UserAlgorithms.append ( bsel ) 
```
{% challenge "Challenge" %}
Try to code some MC-truth algorithm, that get some true MC-decays, and fill simple n-tuple/tree with some simple kinematical information on some of decay products.  Try to select as an example the _ordinary_ `ALLSTREAMS.DST`  (not Turbo version!). Processing of `ALLSTREAMS.DST/Turbo` and  various kinds of `ALLSTREAMS.MDST` and `MC-Turbo` requires a bit different configurtaion steos, that we'll discuss later. For a time being y ou can use e.g. the MC-file `'/lhcb/MC/2012/ALLSTREAMS.DST/00033494/0000/00033494_00000013_1.allstreams.dst'`, that contains the true MC-decays  _Bs -> J/psi K+ K- pi+ pi-_ with many intermediate resonances. 
{% solution "Solution" %}
The complete module, that processes the events of `[B_s0 ==> J/psi(1S) K+ K- pi+ pi-]CC` with very rich structure of intermediate resonances is available [here](https://gist.github.com/VanyaBelyaev/93a8aff4d305e3e68b3990142b133d0f)
{% endchallenge %}
