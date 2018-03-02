# The first Bender algorithms 

{% prereq "Prequisites" %}
* One needs to undertand the stucture of Bender _module_ : `run`, `configure` functions 
and the `__main__` clause
* One needs to know the sctructire of `configure` function
{% endprereq %}

{% objectives "Learning objectives" %}
* Understand Bender algorithms
  * How to code them?
  * How to embedd them into the application?  
{% endobjectives %}


## _Hello, world!_ 

Traditionally for tutorials, the first algorithm prints `Hello, world`. 
The Bender algorithm inherits from the class `Algo`,  imported from `Bender.Main` module.
This python base is indeed a `C++`-class, that inherits from 
`LoKi::Algo` class, that in turn inherits from `DaVinciTupleAlgorithm`. 
The simplest algorithm is rather trivial:
```python
from Bender.Main iport Algo, SUCCESS 
class HelloWorld(Algo):
    """The most trivial algorithm to print 'Hello,world!'"""
    def analyse( self ) :   ## IMPORTANT! 
        """The main 'analysis' method"""        
        ## use the native Python print to stdout:
        print       'Hello, world! (using native Python)'
        ## use Gaudi-style printout:
        self.Print( 'Hello, World! (using Gaudi)')
        return SUCCESS      ## IMPORTANT!!! 
```
Important note:
 - one _must_ implement the method `analyse` that gets no argument and return `StatusCode`

Optionally one can (re)implement other important methods, like `__init__`,
`initialize` , `finalize`, etc...
In particular `initialize` could be used to locate some _tools_ and or pre-define some
useful code  fragments, e.g. some _expensive_ LoKi-functors.


{% discussion "Where to put the algorithm code?" %}
It is recommended to put the algorithm code directly in the main body of yoru module, 
outside of __configure__ function. It allows to have visual separation of algorithmic and 
configuration parts.  Also it helps for independent reuse of both parts. 
{% enddiscussion %}

### How to embedd the algorithm into the application ?

There are two approaches _brute-force_, that works nicely with such primitive code 
as `HelloWorld` algorithm above and the intelligent/recommended approach, that smoothly insert the algorithm into the overall flow of algorithms, provdied by `DaVinci`

#### _Brute-force_

One can instantiate the algorithm in __configure__ method  **afte** instantiation of application manager, and add the algorithm, into the list of top-level algorithms, known to Gaudi:
```python
gaudi = appMgr() 
alg   = HelloWorld('Hello')
```

   


