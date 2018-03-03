# Handling of `TisTos` information in Bender  (`BenderTools.TisTos` module)


Bender offers set of methods to handle `TisTos`-information in (relatively) easy way.
This functionality  comes from `BenderTools.TisTos` module.
In short, it adds three relates method in the base class `Algo`:

|  Method           |  Short description   | 
| ---               |  ---                 | 
| `decisions`       | collect the trigger decisions for the given particle  | 
| `trgDecs`         | print the collected trigger statistics in readable way   |  
| `tisTos`          | fill N-tuple with `TisTos`-information    | 

All of them are (relatively well) documented and one can easily inspect them:
```python
import BenderTools.TisTos
from Bender.Main import Algo
help(Algo.decisions)
```

## How to know what trigger lines are relevant or the given decay/particle?

Often information about the releavant trigger lines are spread in corridors 
in a form of myths, general beliefs or, in the best case, references to ANA-notes 
for some similar analysis. However it is very simple to collect 
this infomation using Bender. The method `decisions` is our friend here. 
the usage of  this method require some preparatory work for the algorithm, 
namely one needs to instrument `initialize`-method:
```python
class TrgLines(Algo):
    """Collect infomation about the trigger lines relevant for certain decays/particles
    """
    def initialize ( self ) :

        sc = Algo.initialize ( self ) ## initialize the base class
        if sc.isFailure() : return sc

        #
        ## container to collect trigger information, e.g. list of fired lines 
        #
        triggers = {}
        triggers ['psi'] = {} ## slot to keep the information for J/psi 
        triggers ['K']   = {} ## slot to keep the information for kaons  
        triggers ['B']   = {} ## slot to keep the information for B-mesons 
        
        sc = self.tisTos_initialize ( triggers , lines = {} )
        if sc.isFailure() : return sc

        return SUCCESS
```
Here in this example we want to collect `TisTos`-information 
for `J/psi`, `K` and the whole `B+`-meson. Then in the main `analyse`-method one just needs to invoke 
the method `decisions` for each particles  in interest:
```python
def analyse( self ) :   ## IMPORTANT! 
  """The main 'analysis' method """
  ...         
  for b in particles :
            
      psi =b(1) ## the first  daughter: J/psi 
      k   =b(2) ## the second daughter: K 
            
      ## collect trigger information for J/psi 
      self.decisions   ( psi , self.triggers['psi'] )

      ## collect trigger information for kaons 
      self.decisions   ( k   , self.triggers['K']   )
            
      ## collect trigger information for B-mesons 
      self.decisions   ( b   , self.triggers['B']   ) 
  
  ...     
  return SUCCESS   
```
Thats all. Then when jobs runs  it dumps to the log-file the running trigger statistics, 
and the statistics is dumped into the file `TrgLines_tistos.txt` (`<ALGONAME>_tistos.txt` in general) 
amd into `shelve`-dbase `TrgLines_tistos.db` (`<ALGONAME>_tistos.db` in general).
The summary table looks like:
```
******************************************************************************************
 Triggers for  psi
******************************************************************************************
Hlt1_TIS psi   #lines:     7 #events 321   
 (  0.62 +- 0.44 )       Hlt1DiMuonHighMassDecision 
 (  0.62 +- 0.44 )       Hlt1DiMuonLowMassDecision 
 ( 19.00 +- 2.19 )       Hlt1TrackAllL0Decision 
 (  3.74 +- 1.06 )       Hlt1TrackAllL0TightDecision 
 (  4.36 +- 1.14 )       Hlt1TrackMuonDecision 
 (  0.93 +- 0.54 )       Hlt1TrackPhotonDecision 
 (  0.31 +- 0.31 )       Hlt1VertexDisplVertexDecision 
 (100.00 +- 0.31 )       TOTAL 
Hlt1_TOS psi   #lines:     9 #events 321   
 ( 72.90 +- 2.48 )       Hlt1DiMuonHighMassDecision 
 ( 59.81 +- 2.74 )       Hlt1DiMuonLowMassDecision 
 (  0.31 +- 0.31 )       Hlt1DiProtonDecision 
 (  9.03 +- 1.60 )       Hlt1SingleMuonHighPTDecision 
 (  0.31 +- 0.31 )       Hlt1SingleMuonNoIPDecision 
 ( 44.24 +- 2.77 )       Hlt1TrackAllL0Decision 
 ( 10.90 +- 1.74 )       Hlt1TrackAllL0TightDecision 
 ( 74.77 +- 2.42 )       Hlt1TrackMuonDecision 
 (  0.31 +- 0.31 )       Hlt1TrackPhotonDecision 
 (100.00 +- 0.31 )       TOTAL 
Hlt1_TPS psi   #lines:     6 #events 321   
 ( 13.71 +- 1.92 )       Hlt1DiMuonHighMassDecision 
 ( 10.90 +- 1.74 )       Hlt1DiMuonLowMassDecision 
 (  0.31 +- 0.31 )       Hlt1DiProtonDecision 
 (  3.12 +- 0.97 )       Hlt1TrackAllL0Decision 
 (  0.93 +- 0.54 )       Hlt1TrackAllL0TightDecision 
 (  4.67 +- 1.18 )       Hlt1TrackMuonDecision 
 (100.00 +- 0.31 )       TOTAL 
```
Only a short fragment is shown here, one gets similar fragments for all declared 
particles (`psi`, `K` and `B`) and for all trigegr levels (`L0`, `Hlt1` and `Hlt2`).
The full table is accessible [here](https://gist.github.com/VanyaBelyaev/90cc0f9b1ab84c8e40e2cd2ccf321bc3)
The content of the summaty table is rather intuitive: it summarizes the fire frequences for 
varios trugegr lines for three regimes `TIS`, `TOS` and `TPS`. 
Inspecting such table, one can immediately conclude that 
the possible choice of trigger `Hlt1-TOS` lines is 
 - `Hlt1DiMuonHighMassDecision`
 - `Hlt1DiMuonLowMassDecision`
 - `Hlt1TrackAllL0Decision`
 - `Hlt1TrackMuonDecision`
Other `Hlt1-TOS`-lines are  not very relevant. 
But pleaae note that here only vey small statistics is used (321 event), 
and with larger statistics some conclusion coudl be corrected.
E.g. due to  small statistics here, for `Hlt1-TIS`-lines the choice is not evident:
one clearly see that `Hlt1TrackAllL0Decision` line is important, but for 
importance of other lines one csn judge only after the 
significant increase of the statistics.

{% discussion "What is `<ALGNAME>_tistos.db` file?" %}
The trigger statistics is  saved not only in `<ALGNAME>_tistos.txt`-file but also in `shelve`-dbase 
`<ALGNAME>_tistos.db`.  If really large statistics is required there are some utilities to merge the 
information from these `<ALGNAME>_tistos.db` together, e.g. on the output of Ganga jobs.
{% enddiscussion %}

{% challenge "Challenge" %}
  1. Add `decisions`-function into your into your previous Bender module with n-tuples. (Do not  forget to instrument `initialize` method!).
  2. Run it and observe the output summary table 
  3. Identify the relevant  `L0-TOS`, `Hlt1-TOS` and `Hlt2-TOS` lines for your decay 
     * Does it correspond to your  expectations?
{% solution "Solution" %}
The complete module is accessible [here](https://gist.github.com/VanyaBelyaev/0e12d07b705dcd4af48aea8dad02f727)
and the corresponsing summary table is [here](https://gist.github.com/VanyaBelyaev/90cc0f9b1ab84c8e40e2cd2ccf321bc3)
{% endchallenge %}
