# Advanced fill of n-tuples 

The n-tuple filling functionality, described [above](getting-started/firstalgorithms.md) is 
drasticlaly extended using the functions from `BenderTools.Fill` module.
The import of this module  add following functions to the base class `Algo`:

|  Method           |  Short description   | 
| ---               |  ---                 | 
| `treatPions`      | add information about pions    |
| `treatKaons`      | add information about kaons    | 
| `treatProtons`    | add information about protons  | 
| `treatMuons`      | add information about muons    | 
| `treatPhotons`    | add information about photons  | 
| `treatDiGammas`   | add information about di-photons (pi0, eta,...)  | 
| `treatTracks`     | add information about the tracks   | 
| `treatKine`       | add detailed kinematic information for the particle  | 
| `fillMasses`      | masses of sub-combinations   | 
| `addRecSummary`   | add rec-summary information   | 
| `addGecInfo`      | add some GEC-info  | 

These methods can be considered as a kind of  very light _tuple-tools_.
All of them are (well) documented and one can easily inspect them:
```python
import BenderTools.Fill
from Bender.Main import Algo
help(Algo.treatPions)
```
Also all these methods  print  detailed how-to infomratino  in log-file  at the moment of the first invoke, and it vasn be very helpful to understand the branches in n-tuple/tree.  
The typical usage of these methods is: 
```python
tup = self.nTuple('MyTuple')
for p in particles :
            
    psi = p(1) ## the first daughter: J/psi 
            
    ## fill few kinematic variables for the particles:
    self.treatKine   ( tup , p   , '_b'   )  ## use the suffix to mark variables 
    self.treatKine   ( tup , psi , '_psi' )  ## use the suffix to mark variables 
            
    self.treatKaons  ( tup , p ) ## fill some basic information for all kaons
    self.treatMuons  ( tup , p ) ## fill some basic information for all muons
    self.treatTracks ( tup , p ) ## fill some basic information for all charged tracks

    tup.write()     
```
{% challenge "Challenge" %}
  1. Add (some of) these functions into your previous Bender module with n-tuples.
  2. Run it and observe the detailed  printout in log-file 
  3. Observe new variables in your n-tuple/tree and find their description in the log-file or via `help(Algo.<THEMETHOD>)`
     * Is the description for all new varibales clear enough? 
{% solution "Solution" %}
The complete module is accessible [here] (https://gist.github.com/VanyaBelyaev/becc26fe5dea90aa96cb8f929faf6a53)
{% endchallenge %}



