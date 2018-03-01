# The  first  two _useless_, but _illustrative_  examples 
  

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
can already be submitted to Grid, and Ganga will classify it as valid Bender code.
{% discussion "The details for the curious students:" %}
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


<script src="https://gist.github.com/VanyaBelyaev/328a015a409ebe3c04f94feba8f9e16f.js"></script>
<script src="https://gist.github.com/VanyaBelyaev/328a015a409ebe3c04f94feba8f9e16f.js?file=gist.md"></script>

<script src="https://gist.github.com/benbalter/5555251.js"></script>
<script src="https://gist.github.com/benbalter/5555251.js?file=gist.md"></script>


Such that the whole script looks  as:   https://gist.github.com/VanyaBelyaev/328a015a409ebe3c04f94feba8f9e16f.js
