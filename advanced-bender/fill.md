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