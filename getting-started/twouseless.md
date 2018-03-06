# The first two _almost useless_, but _very important_ examples 

{% objectives "Learning objectives" %}
* Understand the overall structure of Bender _module_ and the configuration of the application 
{% endobjectives %}

## _Do-nothing_

{% objectives "Learning objectives" %}
* Understand the overall structure of Bender _module_ using the oversimplified example
{% endobjectives %}

Any _valid_ Bender module must have two essential parts 

 - function `run`  with the predefined signature 
 - function `configure` with the predefined signature 

For the most trivial (_"do-nothing"_) scenario function `run` is
```python
def run ( nEvents ) :
    # some fictive event loop 
    for i in range( 0 , min( nEvents , 10 ) ) :  print ' I run event %i ' % i        
    return 0
```
In a similar way, the simplest _"do-nothing"_-version of `configure`-function is 
```python
def configure (  datafiles ,  catalogs = [] , castor = False , params = {} ) :   
    print 'I am configuration step!'
    return 0
``` 
As one clearly sees, these lines do nothing useful, but they are perfectly enough
to be classified as the first Bender code. 
Moreover, the python module with these two function
can already be submitted to Ganga/Grid, and Ganga will classify it as valid Bender code.
Therefore this code is already _"ready-for-Ganga/Grid"_!
{% discussion "The details for the curious students: how Ganga/Grid treat Bender modules?" %}
Actually Ganga executes at the remote node the following wrapper code
```python
files    = ... ## this one comes from DIRAC
catalogs = ... ## ditto 
params   = ... ## extra parameters (if needed): this comes from the user
nevents  = ... ## it comes from Ganga configuration

import USERMODULE   ## here it imports your module! 
USERMODULE.configure ( files , catalogs ,  params = params )  
USERMODULE.run       (  nevents )
```
Thats all! From this snippet you see:
 - the code must have the structure of python _module_, namely no executable lines should appear in the main body of the file  
    - (note  the difference with respect to the _script_)
 - it must have two functions `run` and `configure`  
    - (everything else is not used)
{% enddiscussion %}
The whole module is here:
<script src="https://gist.github.com/VanyaBelyaev/328a015a409ebe3c04f94feba8f9e16f.js"></script>


In practice, before the submission the jobs to Ganga/Grid, the code needs to be tested using some test-data. 
This, formally unnecessary, but very important step can be easily embedded into your module using 
python's `__main__` clause:
```python
if '__main__' == __name__ : 
    print 'This runs only if module is used as the script! '
    configure ( [] , catalogs = [] , params = {} )    
    run ( 10 ) 
```
Note that these lines effectively convert the _module_ into _script_, and finally one gets: 
<script src="https://gist.github.com/VanyaBelyaev/4d96dbfa8e94379b284ec7364365dde6.js"></script> 


### How  to run it interactively? 

The answer is trivial:
```bash
lb-run Bender/prod python DoNothing.py
```
That's all. Make a try and see what you get!

{% discussion "Unnecessary but very useful decorations:" %}
It is highly desirable and _recommended_ to put some _"decorations"_ a top of this minimalistic lines:
 - add magic  `#!/usr/bin/env python` line as the top line of the module/script 
 - make the script executable: `chmod +x ./DoNothing.py`
 - add a python documentation close to the begin of the script
   - fill some useful python attributes with the proper information
      * `__author__`
      * `__date__`
      * `__version__`
   - do not forget to add documentation in Doxygen-style and use in  comments following tags 
      *  `@file`
      *  `@author`
      *  ... 

With all these decorations the complete module is [here](https://gist.github.com/VanyaBelyaev/1deeb39959f44689f054006c290d1432)
{% enddiscussion %}

For all subsequent lessons we'll gradually extend this script  
with the additional functionality,  step-by-step converting 
it to something much more useful.   
 
{% discussion "In practice, ..." %}
In practice, the prepared and _ready-to-use_ function `run` is imported from some of the main Bender module `Bender.Main`,
and the only one really important task for the user is to code the function `configure`.
{% enddiscussion %}

## _DaVinci_

{% objectives "Learning objectives" %}
* Understand the internal structure of the _configure_ function
{% endobjectives %}

For the _typical_ case in practice, the function _configure_ (as the name suggests) contains three parts 
 1. _static configuration_: the configuration of `DaVinci` configurable                        (almost  unavoidable)
 2. _input data and application manager_:  define the input data and instantiate Gaudi's application manager  (mandatory) 
 3. _dynamic configuration_: the configuration of `GaudiPython` components              (optional)

### _Static configuration_ 

For the first part, the instantiation of DaVinci configurable is almost unavoidable step:
```python
from    Configurables import DaVinci
rootInTES = '/Event/PSIX'
dv = DaVinci ( DataType   = '2012'    ,
               InputType  = 'MDST'    ,
               RootInTES  = rootInTES )
```
Here we are preparing application to read `PSIX.MDST` - uDST with few useful selections for B&Q Working Group.
Note that in this part one can use all power of DaVinci/Gaudi `Congifurables`. 
In practice, for physics analyzes, it is very convenient to use here `Selection` framework, 
that  allows to configure `DaVinci` in a very compact, safe, robust and nicely  readable way, 
e.g. let's get from Transient Store some `selection` and print its content
```python
from PhysConf.Selections import AutomaticData,  PrintSelection
particles = AutomaticData  ( 'Phys/SelPsi2KForPsiX/Particles' ) 
particle  = PrintSelection ( particles )  
```       
As the last sub-step of (1), one needs to pass the selection object to `DaVinci`
```python
dv.UserAlgorithms.append ( particles )
```
{% discussion "Where is `SelectionSequence` ?" %}
The underlying `SelectionSequence` object will be created automatically. 
You should not worry about it. 
{% enddiscussion %}


### _Input data and application manager_ 

This part is rather trivial  and almost always standard:
```python
from Bender.Main import setData, appMgr 
## define input data 
setData  ( inputdata , catalogs , castor )
## instantiate the application manager 
gaudi = appMgr()  ## NOTE THIS LINE!  
```
while `setData` can appear  anywhere inside `configure` function, 
the line with `appMgr()` is very special.  After this line, 
no _static configuration_ can be used anymore. Therefore all the code dealing with 
`Configurables` and `Selections` must be placed above this line. 

### _Dynamic configuration_

For this particular example, it is not used,  but will be discussed further in conjunction with other lessons.


The complete `configure` function is:
<script src="https://gist.github.com/VanyaBelyaev/f335248a874f65f9dabdbd151a526e47.js"/></script>
 
The prepared and _ready-to-use_ function `run` is imported `Bender.Main`:
```python
from Bender.Main import run 
```

Now our Bender module (well, it is actually pure `DaVinci`, no real Bender here!) is  ready to be used with Ganga/Grid.
For local interactive tests we can use the trick with `__main__` clause:
The `__main__` clause in our case contains some input data for local tests:
```python
if __name__ == '__main__' :
    inputdata = [
        '/lhcb/LHCb/Collision12/PSIX.MDST/00035290/0000/00035290_00000221_1.psix.mdst' ,
        '/lhcb/LHCb/Collision12/PSIX.MDST/00035290/0000/00035290_00000282_1.psix.mdst' ]
    configure( inputdata , castor = True )
    ## the event loop 
    run(10000)
```
The complete moodule can be accessed [here](https://gist.github.com/VanyaBelyaev/01cce773db3915883c6f2cc919bfac71)

### How to run it?
Again, the answer is trivial (and universal):
```bash
lb-run Bender/prod python DoNothing.py
```
That's all. Make a try and see what you get!

{% challenge "Challenge" %}
Try to convert any of your existing `DaVinci` simple _script_ into Bender _module_ and run it interactively.
You can use the result of this excersize for subsequent lessons. 
{% endchallenge %}


{% discussion "What is `castor` ? Why `LFN` is used as input file name?" %}
Bender is smart  enough, and for many cases it can efficiently convert input `LFN` into 
the real file name.
  1. First, if you have Grid proxy enabled (`lhcb-proxy-init`) is uses internally `LHCbDirac` to locate and access the file. 
This way is not very fast, but for all practical cases  this look-up is almost always successful, 
however for some cases certain hints could be very useful.
In particular, you can specify the list of Grid sites to look for data files: 
```python
## define input data 
setData  ( inputdata , catalogs , castor = castor ,  grid = ['RAL','CERN','GRIDKA'] )
```
  2. Second, for CERN, one can use option `castor = True`, that activates the 
local look-up on input files at CERN-CASTOR and CERN-EOS storage
(`root://castorlhcb.cern.ch` and `root://eoslhcb.cern.ch`).
This look-up is much faster than the first option, 
but here the success is not guaranteed, since not all files have their replicas at CERN.
  3. Third, for access to special locations, e.g. some local files, Bender also makes a try to look into 
     directories specified via the environment variable `BENDERDATAPATH` (column separated list of paths)
     and also  try to contruct the file names using the content of environment variable `BENDERDATAPREFIX` 
     (semicolon separated list of prefixes used for construction the final file name).
     The  file name is constructed using all `(n+1)*(m+1)` variants, where `n` is number of items in 
     `BENDERDATAPATH` and  `m` is number of items in `BENDERDATAPREFIX`.
     Using the combination of  `BENDERDATAPATH` and `BENDERDATAPREFIX` variables one can make very powerful 
     matching of _short_ file names (e.g. LFN) to the actual file.
     Using these variables one can easily perform 
     a local and efficient access to Grid files from some _close_ Tier-1/2 center.   
{% enddiscussion %}


{% keypoints "Keypoints" %}
With these two examples, you should aready be able to 
* code the _valid_ (but useless)  Bender modules 
* run them interactively
{% endkeypoints %}
