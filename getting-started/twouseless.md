# The  first  two almost _useless_, but highly _illustrative_ examples 

{% objectives "Learning objectives" %}
* Understand the overall structure of Bender _module_ and the configuration of the application 
{% endobjectives %}

## _Do-nothing_

{% objectives "Learning objectives" %}
* Understand the overall structure of Bender _module_ using the oversimplified example
{% endobjectives %}

Any _valid_ Bender module must have two essential parts 

 - function `run`  with the predefined signature 
 - function `configure` with the predefined dignature 

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
    - (everythnig else is not used)
{% enddiscussion %}
The whole module is here:
<script src="https://gist.github.com/VanyaBelyaev/328a015a409ebe3c04f94feba8f9e16f.js"></script>


In practice, before the submission the jobs to Ganga/Grid, the code needs to be tested using some test-data. 
This, formally unnesessary, but very important step can be easily embedded into your module using 
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

{% discussion "Unnesessary but very useful decorations:" %}
It is highly desirable and _recommended_ to put some _"decorations"_ a top of this minimalistic lines:
 - add magic  `#!/usr/bin/env python` line as the top line of the module/script 
 - make the script executable: `chmod +x ./DoNothing.py`
 - add a python documentation close to the begin of the script
   - fill some useful python attributes with the proper informaton
      * `__author__`
      * `__date__`
      * `__version__`
   - do not forget to add documenation in Doxygen-style and use in  comments following tags 
      *  `@file`
      *  `@author`
      *  ... 

With all these decorations the complete module is [here](https://gist.github.com/VanyaBelyaev/1deeb39959f44689f054006c290d1432)
{% enddiscussion %}

For all subsequent lessons we'll gradually fill this script  
with the additional functionality,  step-by-step converting 
it to something much more useful.   
 
{% discussion "In practice, ..." %}
In practice, the prepared and _ready-to-use_ function `run` is imported from some of the main Bender module `Bender.Main`,
and the only one really important task for the user is to code the function `configure`.
```
{% enddiscussion %}

## _DaVinci_

{% objectives "Learning objectives" %}
* Understand the content of the _configure_ function
{% endobjectives %}

For the _typical_ case in practice, the function _configure_ (as the name suggests) contains three parts 
 1. configuration of `DaVinci` configurable                        (almost  unavoidable)
 2. define input data and instantiate Gaudi's application manager  (mandatory) 
 3. dynamic configuration of `GaudiPython` components              (optional)
 
For the first part, the instantiation of  DaVinci configurable is alsmost unavoidable step:
```python
from    Configurables import DaVinci
rootInTES = '/Event/PSIX'
dv = DaVinci ( DataType   = '2012'    ,
               InputType  = 'MDST'    ,
               RootInTES  = rootInTES )
```
Here we are preparing application to read `PSIX.MDST` - uDST with few useful selections for B&Q Working Group.
Note that in this part one can use all power of DaVinci/Gaudi `Congifurables`. 
In practice, for physics analyses, it is veyr convinient to use here `Selection` framework, that 
allows to configure `DaVinci` in a very compact, safe, robust and nicely  readable way, e.g.
let's get from Transient Store some `selection` and print its content
```python
from PhysConf.Selections import AutomaticData,  PrintSelection
particles = AutomaticData  ( 'Phys/SelPsi2KForPsiX/Particles' ) 
particle  = PrintSelection ( particles )  
```       
As the last sub-step of (1), one needs to pass the seelction to `DaVinci`
```python
dv.UserAlgorithms.append ( particles )
```
{% discussion Where is `SelectionSequence` ? %}
The underlying `SelectionSequence` object will be created automatically. 
You should not worry about it. 
```
{% enddiscussion %}
  


{% discussion "In practice, ..." %}
In practice, the prepared and _ready-to-use_ function `run` is imported from some of the main Bender module `Bender.Main`,
and the only one really important task for the user is to code the function `configure`.
The `__main__` clause usually contains some input data for local tests:
```python
if __name__ == '__main__' :
    inputdata = [
        '/lhcb/LHCb/Collision12/PSIX.MDST/00035290/0000/00035290_00000221_1.psix.mdst' ,
        '/lhcb/LHCb/Collision12/PSIX.MDST/00035290/0000/00035290_00000282_1.psix.mdst' ]
    configure( inputdata , castor = True )
    ## the event loop 
    run(10000)
```
{% enddiscussion %}
