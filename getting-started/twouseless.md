# The  first  two almost _useless_, but highly _illustrative_ examples 

{% objectives "Learning objectives" %}
* Understand the overall structure of Bender _module_ using oversimplified examples 
{% endobjectives %}

## _Do-nothing_

Any _valid_ Bender module must have essential parts 

 - function `run`  with the predefined signature 
 - function `configure` with the predefiend dignature 

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
to be classified as the frist Bender code. Moreover the python module with these two function
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
 - the code must have a stricture of python _module_  
    - (note  the difference with respect to the _script_)
 - it must have two functions `run` and `configure`  
    - (everythnig else is not used)
{% enddiscussion %}
The whole script is here:
ququ
<script source="https://gist.github.com/VanyaBelyaev/328a015a409ebe3c04f94feba8f9e16f.js?file=gist0.md"></script>
ququ1
<script source="https://gist.github.com/VanyaBelyaev/328a015a409ebe3c04f94feba8f9e16f.js"></script>

In practice, before the submission the jobs to Ganga/Grid, the code needs to be tested using some test-data. 
This, formally unnesessary, but very important step can be easily embedded into your module using 
python's `__main__` clause:
```python
if '__main__' == __name__ : 
    print 'This runs only if module is used as the script! '
    configure ( [] , catalogs = [] , params = {} )    
    run ( 10 ) 
```

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
   - do not forget to add documenatio in Doxygen-style and use in  comments following tags 
      *  `@file`
      *  `@author`
      *  ... 
{% enddiscussion %}

For all subsequent lessons we'll gradually fill this script  
with the additional functionality,  step-by-step converting 
it to something much more useful.   
 
{% discussion "In practice, ..." %}
In practice, the prepared and _ready-to-use_ function `run` is imported from some of central Bender modules, 
namely `Bender.Main` and the only one really important task for the user is to code the function `configure`.
The `__main__` clause usually contains some input data for local tests:
```python
if __name__ == '__main__' :
    ## job configuration
    ## BKQuery ( '/LHCb/Collision12/Beam4000GeV-VeloClosed-MagDown/Real Data/Reco14/Stripping20/WGBandQSelection7/90000000/PSIX.MDST'   )
    inputdata = [
        '/lhcb/LHCb/Collision12/PSIX.MDST/00035290/0000/00035290_00000221_1.psix.mdst',
        '/lhcb/LHCb/Collision12/PSIX.MDST/00035290/0000/00035290_00000282_1.psix.mdst',
        ]
    
    configure( inputdata , castor = True )
    ## the event loop 
    run(10000)
```
{% enddiscussion %}

## _Hello, world!_
