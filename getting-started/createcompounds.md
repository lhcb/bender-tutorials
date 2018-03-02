# Create the compound particles in Bender

{% prereq "Prequisites" %}
* One needs to undertand the way how Bender accesses the data 
{% endprereq %}

{% objectives "Learning objectives" %}
* Understand how Bender algorithm combines the particles and create compound particles   
{% endobjectives %}

##  _Make B_ 

The next example illustrates how one combines the particles and create 
the compound particles inside the Bender algorithm. 
Let's consider a simple case of creation of `B+ -> J/psi(1S) K+` decays.

The first step is  rather obvious: before getting the combinations, 
we need to get the individual components. 
Here `select` function  does the job:
```python
## get J/psi mesons from the input
psis  = self.select ( 'psi' , 'J/psi(1S) -> mu+ mu-' )
## get energetic kaons from the input:
kaons = self.select ( 'k'  , ( 'K+' == ID ) & ( PT > 1 * GeV ) &  ( PROBNNk > 0.2 ) )
```
The loop over `psi k` combinations is  ratehr   trivial: 
```python
## make a loop over J/psi K combinations :
for b in self.loop( 'psi k' , 'B+' ) :
    ## fast evaluation of mass (no fit here!) 
    m12 = b.mass ( 1 , 2 ) / GeV
    print 'J/psiK mass is %s[GeV]'  % m12  
    p1  = b.momentum ( 1 ) / GeV 
    p2  = b.momentum ( 2 ) / GeV 
    p12 = b.momentum ( 1 , 2 ) / GeV 
    print 'J/psiK  momentum is is %s[GeV]'  % p12  
```
Looping object (`b` here), as the name,  suggests, make a loop over 
all `psi k` combinations. The loop is done in CPU efficient way,    
and no expensive vertex  fitting is performed.  One can estimate 
various _raw_ (no  fit) kinematical quantities using  functions `momentum`, 
`mass` , etc...  (Note that indices starts from `1`. For all  LoKi-based functions the 
index `0`  is  reserved for _self-reference_, the mother particle itself).
These `raw` quantities can be used for quick reject of _bad_ combinations
before making  CPU-expensive vertex fit.  
If/when   combination satisfies certain criteria, the vertex  
fit and creatino of  the compound particle is triggered automatically 
if  any of particle/vertex information is retrieved (either directy via 
`particle/vertex` method, 
or indirectly, e.g. via call to any _particle/vertex LoKi-functor_.
The good created _mother_ particles can be saved for 
subsequent steps under some unique tag:  
```python
for b in self.loop( 'psi k' , 'B+' ) :
    ## fast evaluation of mass (no fit here!) 
    m12 = b.mass ( 1 , 2 ) / GeV
    if not 5 < m12 <   6    : continue  
    chi2vx = VCHI2 ( b )   ## indirect call for vertex fitr and creation of B+ meson
    if not 0<= chi2v <  20  : continue 
    m = M ( p ) / GeV 
    if not 5 <  m < 5.6     :  continue 
    m.save('MyB')
 
```
Obviously the looping can be combnied with filling of historgams and n-tuples.

{% discussion "How to deal with charge conjugation?" %}
One can make two loops: 
```python
psis    = self.select ( 'psi' , 'J/psi(1S) -> mu+ mu-' )
kplus   = self.select ( 'k+'  , ( 'K+' == ID ) & ( PT > 1 * GeV ) &  ( PROBNNk > 0.2 ) )
kminus  = self.select ( 'k-'  , ( 'K-' == ID ) & ( PT > 1 * GeV ) &  ( PROBNNk > 0.2 ) )

bplus   = self.loop( 'psi k+', 'B+' ) ## the first  loop object 
bminus  = self.loop( 'psi k-', 'B-' ) ## the second loop object 
for cc in ( bplus , bminus ) : 
   for b in cc : 
      m12 = b.mass(1,2) / GeV 
      ...
      b.save('MyB')
```
The popular alternative is _charge-blind_ loops, 
that are a bit simpler, but require some accuracy:
```python
psis    = self.select ( 'psi' , 'J/psi(1S) -> mu+ mu-' )
## ATTENTION: select both K+ and K-, note ABSPID here
k       = self.select ( 'k'   , ( 'K+' == ABSID ) & ( PT > 1 * GeV ) &  ( PROBNNk > 0.2 ) )

for b in self.loop ( 'psi k' , 'B+' ) 
      m12 = b.mass(1,2) / GeV 
      ...
      psi = b(1) ## get the first daughetr 
      k   = b(2) ## get the second daughter

      ## ATTENTION: redefine PID on-flight 
      if Q( k ) > 0 : b.setPID( 'B+' )
      else          : b.setPID( 'B-' )

      b.save('MyB')
```
{% enddiscussion %}   


The saved particles can be extracted back using the method `selected`:
```python
myB = self.selected('MyB')
for b in myB : 
   print M(b)/GeV  
```

###  _Configuration_ 

It is clear that  to build `B+ -> J/psi(1S) K+` decays, one needs to  
feed  the algorithm with J/psi(1S)-mesond and kaons.
Using `Selection`machinery is the most efficient ansd transaprent way to do it.
```python
from PhysConf.Selections import AutomaticData
jpsi = AutomaticData( '/Event/Dimuon/Phys/FullDSTDiMuonJpsi2MuMuDetachedLine/Particles' )
    
from StandardParticles   import StdLooseKaons as kaons     
bsel = BenderSelection ( 'MakeB' , [ jpsi , kaons ] )
```

The complete example of creation of `B+ -> J/psi(1S) K+` decays starting from `DIMUON.DST` 
 is accessible from [here](https://gist.github.com/VanyaBelyaev/c3f2df0e0f23221b4fe7fd74d27fb287)

  
{% keypoints "Keypoints" %}
Wth these example, you should be able to do  
* code Bender _algorithm_ that   perofem loopint, combianig anc creation of compound particles 
{% endkeypoints %}





