# Processing of simulated data in Bender

Processing of simulation data in Bender is rather simple, one just needs to inherit the algortihm from base class `AlgoMC`, this class can be imported from  `Bender.MainMC` module.
```python
from Bender.MainMC import *  # it imports also the whole content of Bender.Main module 
```   

## Important notes: `Simulation=True` and `DDDB/SIMCOND`-tags 

  * One needs to use `Simulation=True` flag for `DaVinci`-configurable
```python
from Configurables import DaVinci
dv = DaVinci ( Simulation      = True           , ## <--- HERE!
               ...
               TupleFile       = 'MCtruth.root' )
``` 
  * It is *very* important to specify the correct `DDDB/SIMCOND`-tags for the simulated data. It is very easy to get efficinecied wrong upto 30% if simulated data a processed with the wrong `DDDB/SIMCOND`-tags. 
```python
from Configurables import DaVinci
dv = DaVinci ( Simulation      = True                      , ##
               ...
               DDDBtag         = 'dddb-20130929-1'         , ## <--- HERE!
               CondDBtag       = 'sim-20130522-1-vc-mu100' , ## <--- HERE!
               ...
               TupleFile       = 'MCtruth.root' )
```

Correct `DDDB/SIMCOND`-tags can be retrived in several  ways: 
  1. from `bookkeeping-DB` for the given production type
{% challenge "Challenge (only for those who knows how to do it)" %}
Do you know how to do it? If so make a try to use  this way. Please use the timer for comparison. 
{% endchallenge %}
  2. using the helper Bender scripts `get-dbtags` or `get-metainfo` for the given file 
{% challenge "Challenge" %}
Try to use these  scripts form the command line. Start with `get-dbtags -h` and `get-metainfo -h` and follow the instructions.
{% solution "Solution" %}
<script src="https://gist.github.com/VanyaBelyaev/8e316f81caaccda69cb3b7ced2abd5d5.js"/></script>
{% endchallenge %}
  3. Using `dirac-bookkeeping-decays-path` from `LHCbDirac` for the given eventype:
```bash
lb-run -c x86_64-slc6-gcc49-opt LHCbDirac/prod dirac-bookkeeping-decays-path 13104231
``` 
{% challenge "Challenge" %}
Make a try with this command. (Do not forget to obtain valid Grid proxy)
 * Is the output clear enough? 
{% solution "Solution" %}
The output is a list tuples. For each tuple one gets (in order)
   - The path in `bookkeeping-DB`
   - `DDDB`-tag
   - `SIMCOND`-tag
   - Number of files 
   - Number of events 
   - production ID 
<script src="https://gist.github.com/VanyaBelyaev/8f057332459d03bd0ea040b05d124f53.js"/></script>
{% endchallenge %}
  4. for Ganga/Grid there is a way to combine the function `getBKInfo2/getBKInfo` to obtain the information on flight from `bookkeeping-DB` and to propagate this information to Bender using `params`-argument of the `configure`function. This way is built around (3)
{% discussion "In details,..." %}
```python
templ = JobTemplate( 
   application  = prepareBender (
    version      = 'v31r0'                 ,
    module       = my_module               ,
    use_tmp      = True                    ) ,
    ...
    ) 
productions = getBKInfo2 ( 13104231 )
for entry in productions :
    print 'INFORMATION: %s' % entry 
    path      = entry ['path'     ] ## "long path"
    dddbtag   = entry ['DDDBtag'  ] 
    conddbtag = entry ['CondDBtag']

    j = Job (  template ) 
    j.name = ... ## construct name here
    j.inputdata = BKQuery ( path ).getDataset() 
    j.application.params = { 'DDBtag' : dddbtag , 'CondDBtag' : conddbtag } 
    j.submit() 
 ```
where it is assumed that `configure`-function is instrumented properly to accept `params` and to propagate the tags further to `DaVinci`-configurable. The function `getBKInfo2` comes from here:
<script src="https://gist.github.com/VanyaBelyaev/6a6ddd1ff87757ab322b2d6e23b7ede0.js"></script>
{% enddiscussion %}


##  Easy, safe and robust alternative :-)   
In practice, none of the step described above are really needed, since one can just instruct  Bender to obtain the tags directly from the input files.   In this _recommended_ scenario, no `DDDBtag/CondDBtags` to be specified  for `DaVinci`-configurable, but one needs to activate `useDBtags=True` flag for `setData`-function:
```python
dv = DaVinci ( Simulation      = True                      , 
               ...
               ## DDDBtag         = 'dddb-20130929-1'         , ## NOT NEEDED
               ## CondDBtag       = 'sim-20130522-1-vc-mu100' , ## NOT NEEDED
               ...
               TupleFile       = 'MCtruth.root' )
...
setData  ( inputdata , catalogs , castor , useDBtags = True ) ## <--- HERE!
```
This is, probably, the most robust, safe and simultaneously the most convinient way 
to treat `DDDB/SIMCOND`-tags for your application :-) 

  


