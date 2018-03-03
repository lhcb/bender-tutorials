# Handling of `TisTos` information in Bender  (`BenderTools.TisTos` module)


Bender offers set of methods to handle `TisTos`-information in (relatively) easy way.
This functionality  comes from `BenderTools.TisTos` module.
In short, it adds three relates method in the base class `Algo`:

|  Method           |  Short description   | 
| ---               |  ---                 | 
| `decisions`       | collect the trigger decisions for given particle  | 
| `trgDecs`         | print the collected trigger statistics in readable way   |  
| `tisTos`          | fill N-tuple with TisTos information    | 

All of them are (realtively well) documented and one can easily inspect them:
```python
import BenderTools.TisTos
from Bender.Main import Algo
help(Algo.decisions)
```

