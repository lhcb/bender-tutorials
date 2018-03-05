# Using  _tools_ in Bender

Usage of _tools_ and _services_ in Bender  is  rather trivial 
  1. One aquires _tools_ and _services_ usin the method `tool` and `svc` (that are very smilar to thir C++ counterpartners 
`GaudiAlgorithm::tool` and `GaudiAlgorithm::service`). Typically _tools_ and _services_ are aquired in `initialize` method, e.g.  
```python
class TupleTools(Algo):
    """Demonstration how to use TupleTools with Bender"""
    def initialize ( self ) :
        sc = Algo.initialize ( self ) ## initialize the base class
        if sc.isFailure() : return sc        
        IPTT = cpp.IParticleTupleTool
        IETT = cpp.IEventTupleTool        
        self.t1 = self.tool( IPTT,'TupleToolPid'      , parent = self ) ## <--- HERE!
        if not self.t1 : return FAILURE         
        self.t2 = self.tool( IPTT,'TupleToolGeometry' , parent = self ) ## <--- HERE!
        if not self.t2 : return FAILURE
        self.e1 = self.tool( IETT,'TupleToolBeamSpot' , parent = self ) ## <--- HERE!
        if not self.e1 : return FAILURE         
        return SUCCESS
```
  2. Then _tools_ and _services_ can be direcly used in `analyse`-method:
```python
## the main 'analysis' method 
def analyse( self ) :   ## IMPORTANT! 
    ...        
    tup = self.nTuple('MyTuple')    
    for b in particles :            
        psi = b ( 1 ) ## the first daughter: J/psi             
        ## Particle Tuple Tool
        sc = self.t1.fill ( b , psi , 'psi_' , tup )
        if sc.isFailure() : return sc            
        ## Particle Tuple Tool 
        sc = self.t2.fill ( b , b   , 'b_'   , tup )
        if sc.isFailure() : return sc                        
        ## Event Tuple Tool 
        sc = self.e1.fill ( tup )
        if sc.isFailure() : return sc                        
        tup.write() 
```

Note that many _standard tools_ and _standard services_ 
are direclty accessible via the base class `DVAlgorithm`, e.g.
```python
vx_fitter = self.vertexFitter() 
dcalc     = self.distanceCalculator()
pp        = self.ppSvc()  ##  particle property service 
```

 
## Using _tuple tools_ in Bender

{% challenge "Challenge for the fans of tuple tools use" %}
  1. Add set of known _tuple tools_ into your example
     * (Do not  forget to instrument the `initialize` method to aquire tools)
  2. Run it and observe new varianles into n-tuple.
     * Do their names correspond to your  expectations?
{% solution "Solution" %}
The complete module is accessible [here](https://gist.github.com/VanyaBelyaev/f2924b3b5ecff280d79d0d973afdbeeb)
The structure of n-tuple is:
```python
In [7]: from Ostap.Logger import multicolumn
In [8]: f = ROOT.TFile('TupleTools.root','read')
In [9]: t = f.TupleTools.MyTuple
In [10]: print multicolumn ( t.branches() ) 
BeamX              b__ENDVERTEX_COV_  b__ENDVERTEX_Y     b__FDCHI2_OWNPV    b__OWNPV_CHI2      b__OWNPV_XERR      b__OWNPV_ZERR    
BeamY              b__ENDVERTEX_NDOF  b__ENDVERTEX_YERR  b__FD_OWNPV        b__OWNPV_COV_      b__OWNPV_Y         psi__ID          
b__DIRA_OWNPV      b__ENDVERTEX_X     b__ENDVERTEX_Z     b__IPCHI2_OWNPV    b__OWNPV_NDOF      b__OWNPV_YERR    
b__ENDVERTEX_CHI2  b__ENDVERTEX_XERR  b__ENDVERTEX_ZERR  b__IP_OWNPV        b__OWNPV_X         b__OWNPV_Z       
```
{% endchallenge %}
