# How to process `HepMC` information 

To access `HepMC`-information  one can uses the method `gselect` that is very similar to the methods `select` and `mcselect` discussed [above](README.md).
```python
gBs = self.gselect ( 'bs' ,  "[ Beauty => ( D_s+ ==> K- K+ pi+ ) K-]CC ")
```
Again, as selection criteria one can use the [_decay descriptors_](https://twiki.cern.ch/twiki/bin/view/LHCb/FAQ/LoKiNewDecayFinders) or _LoKi HepMC-functors_.

