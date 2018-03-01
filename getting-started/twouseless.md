# The  first  two _useless_, but _illustrative_  examples 
  

Any valid Bender module mast have essential parts 

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
Such that the whole script looks  as: