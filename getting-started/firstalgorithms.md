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
        print       'Hello, world! (using native Python)'
        self.Print( 'Hello, World! (using Gaudi)')
        return SUCCESS      ## IMPORTANT!!! 
```
Important note:
 - one _must_ implement the method `analyse` that gets no argument and return `StatusCode`

Optionally one can (re)implement other important methods, like `__init__`,
`initialize` , `finalize`, etc...
In particular `initialize` could be used to locate some _tools_ and or pre-define some
useful code  fragments, e.g. some _expensive_ or non-trivial LoKi-functors.




{% discussion "Where to put the algorithm code?" %}
It is recommended to put the algorithm code directly in the main body of your module, 
outside of `configure` function. It allows to have visual separation of 
the algorithmic and configuration parts.  Also it helps for independent reuse of both parts. 
{% enddiscussion %}

### How to embedd the algorithm into the application ?

There are two approaches _brute-force_, that works nicely with such primitive code 
as `HelloWorld` algorithm above and the intelligent/recommended approach, that smoothly insert the algorithm into the overall flow of algorithms, provided by `DaVinci`

#### _Brute-force_

One can instantiate the algorithm in __configure__ method  **after** the instantiation of application manager, 
and add the algorithm, into the list of top-level algorithms, known to Gaudi:
```python
gaudi = appMgr() 
alg   = HelloWorld('Hello')
gaudi.addAlgorithm( alg )  
```
For this particular simple case one can also just replace the list of top-level Gaudi algorithms 
with a single `HelloWorld` algorithm:
```python
gaudi = appMgr() 
alg   = HelloWorld('Hello')
gaudi.setAlgorithms( [ alg ] )  
```
{% discussion "More on an optional _dynamic configuration_" %}
As it has been said earlier, the part of `configure` function, placed after `gaudi=appMgr()` line 
corresponds to _dynamic configuration_, 
and here one can continue the further configuration of the algorthm, e.g. 
```python
gaudi    = appMgr() 
alg      = HelloWorld('Hello')
alg.QUQU = 'qu-qu!'  ## define and set some "parameter" 
gaudi.setAlgorithms( [ alg ] )  
```
Later, this new _parameter_ can be accessed e.g. in `analyse` function:
```python
class HelloWorld(Algo):
    """The most trivial algorithm to print 'Hello,world!'"""
    def analyse( self ) :   ## IMPORTANT! 
        """The main 'analysis' method"""        
        print       'Hello, world! (using native Python)', self.QUQU ## use "parameter"
        self.Print( 'Hello, World! (using Gaudi)')
        return SUCCESS      ## IMPORTANT!!! 
```

Such trick is in general a bit fragile, but it is often useful if one has 
several instances of the algorithm that differ only by some  configuration parameter.
```python
alg1 = MyALG ( ... ) 
alg2 = MyALG ( ... ) 
alg3 = MyALG ( ... ) 
alg1.decay_mode = '[D0 -> K-  pi+]CC'
alg2.decay_mode = '[D0 -> K-  K+ ]CC'
alg3.decay_mode = '[D0 -> pi- pi+]CC'
```
{% enddiscussion %}

This approach is very easy and rather intuitive, but is not so easy to insert the algorithm 
into existing non-trivial flow of algorithms without  a danger to destroy the flow.
In this  way one destroys various standard actions, like (pre)filtering, 
luminosity calculation etc., 
therfore it could not be recommended for the real physics analyses, but 
it could be used for some  simple special cases.

#### _Intelligent approach_ 

For _intelligent_ approach one uses `Selection` wrapper for Bender algotithm, `BenderSelection`. 
This wrapper behaves as any other selection-objects, and it lives 
in _static configuration_ part of `configure` function:
```python
from PhysConf.Selections import AutomaticData,  PrintSelection
particles = AutomaticData  ( 'Phys/SelPsi2KForPsiX/Particles' ) 
particle  = PrintSelection ( particles )  

## configuration object for Bender algorithm:
#                              name   , input selections 
hello = BenderSelection ( 'Hello' , inputs = [ particle ]  )  
dv.UserAlgorithms.append ( hello )
```
As the next step in _dynamic configuration_ part of `configure` function
one instantiates the  algoritm taking all the configuration from  the selection-object:
```python
gaudi = appMgr() 
alg   = HelloWorld( hello ) 
```

To complete the module one (as usual) need to combine in the file
 1. implementation of `HelloWorld` algorithm
 2. `configure` function with proper _static_ and _dynamic_ configurations
 3. `__main__` clause
 4. (`run` function is imported from `Bender.Main` module)
    
The complete module can be accessed [here](https://gist.github.com/VanyaBelyaev/82c6b51790a9a692f04569aa51a879d2)

##  _GetData_ 

Well, now your Bender algorithm knows how to print `Hello,world!`. 
Note that it also gets some data: in th epreviosu example we fed 
it with `particles`-selection. Now try to get this data inside 
the algorithm and make first simpel manipulations with data

### `select` method 
The method `select`  is a heart of Bender algorithm. It allows to select/filter 
the particles that satisfies some criteria from the input particles.
The basic usage is:
```python
myB = self.select ( 'myB' , ('B0' ==  ABSID ) | ('B0' ==  ABSID ) )
```
The method returns collection filtered particles  
The first argument is the tag, that will be associated with    selected particles, 
the second    argument is the selection  criteria. 
The tag _*must*_ be unique, and the selection  criteria coudl be in a form of
  - _predicate_:  LoKi-functor that get the particle as  argument and return the boolean value
  - _decay descriptor_, e.g.  'Beauty --> J/psi(1S) K+ K-'. Some componenys of the decay descriptor can be  _marked_, and in this case, only the _marked_ partcles will be selected:
```python
myB = self.select ( 'beauty' , 'Beauty --> J/psi(1S)  K+  K-')
myK = self.select ( 'kaons'  , 'Beauty --> J/psi(1S) ^K+ ^K-')
```

As soon  as one gets  some good, filtered particles there are many possible actions  
### print it!
```python
myB = self.select ( 'myB' , ('B0' ==  ABSID ) | ('B0' ==  ABSID ) )
print myB 
```
### loop
```python
myB = self.select ( 'myB' , ('B0' ==  ABSID ) | ('B0' ==  ABSID ) )
for b in myB : 
    print 'My Particle:', p 
    print 'some quantities: ', M(p) , PT(p) , P(p)  
```

### fill histograms 
```python
myB = self.select ( 'myB' , ('B0' ==  ABSID ) | ('B0' ==  ABSID ) )
for b in myB : 
    #          what        histo-ID    low-edge  high-edge  #bins 
    self.plot( PT (p)/GeV , 'pt(B)'  , 0        , 20       ,  50  ) 
    self.plot( M  (p)/GeV , 'm(B)'   , 5.2      , 5.4      , 100  ) 
    self.plot( M1 (p)/GeV , 'm(psi)' , 3.0      , 3.2      , 100  )
    self.plot( M23(p)/GeV , 'm(KK)'  , 1.0      , 1.050    ,  50  )
```

### fill n-tuple:
```python
myB = self.select ( 'myB' , ('B0' ==  ABSID ) | ('B0' ==  ABSID ) )
t = self.nTuple('TupleName') 
for b in myB : 
    t.column_float ( 'pt'    , PT (p)/GeV) 
    t.column_float ( 'm'     , M  (p)/GeV) 
    t.column_float ( 'm_psi' , M1 (p)/GeV) 
    t.column_float ( 'm_kk'  , M23(p)/GeV) 
    t.write() 
```
{% discussion "For n-tuples..." %}
Sicne n-tuples( ROOT's `TTree` objects) resides in ROOT-file, 
to use n-tuples, one also need to declare the output file for `TTree`s:
The easiest   way is to rely  on `TupleFile` property of `DaVinci`:
```python
dv = DaVinci ( DataType   = '2012'          ,
               InputType  = 'MDST'          ,
               TupleFile  = 'MyTuples.root' ,  ## SEE HERE !!! 
               RootInTES  = rootInTES       )
```
{% enddiscussion %} 
  