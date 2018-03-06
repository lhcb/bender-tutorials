# Match _reco_ with MC-truth

Matching of recontructed candidates with MC-truth is performed using `MCTRUTH` functors, that allows to answer on the _basic_ question: _does this MC-particle makes the contribution to this reconstructed particle?_. Note that this question is different from e.g. _what is MC-truth for this reconstructed particle?_. These are _different_ questions, and therefore one should not misinterpret the answers. For more details see the chapter 15 in [LHCb-2004-023](https://cds.cern.ch/record/721922).

There are helper methods `mcTruth`, that are needed to create the functor `MCTRUTH`
```python
mcK = self.mcselect ( 'mcK' , '[Beauty ==>  J/psi(1S) ^K+ ^K- pi+ pi-]CC' ) ## get true MC-kaons 
trueK = MCTRUTH ( mcK , mc.mcTruth() ) ## <--- HERE: create MCTRUTH    functor 
```
The created object `trueK` is _LoKi fuctor_, that evaluates to `True` for reconstructed particles, that get the contribution from true MC kaons, selected by the `mcselect` method, 
otherwise it evalautes to `False':
```python
reco_kaons = self.select ( ... )
for k in reco_kaons : 
    print ' True(MC-truth matched) kaon?  %s' % trueK ( k ) 
```
Since it is an ordinary _LoKi functor_ is could be combined with all other functors, e.g. one can select from the input only MC-truth matcehd kaons:
```python
truth_matched_kaons = self.select ( 'K' ,  ( 'K+' == ABSID ) & trueK )
``` 
{% discussion "Could it be inverted?" %}
the short answer is _yes, there is inverse for `MCTRUTH`  functor_. 
The inverse functor is `RCTRUTH`, it evalauted for `True` for any MC particle, that makes contribution to the selected recontructed candidate, and `False` otherwise.
```python
B = self.select ( 'B' ,  'Beauty -> J/psi(1S)  K+  K- pi+ pi-' ) 
## get kaons from our reconstrcuted B-candidates 
K = self.select ( 'K' , '[Beauty -> J/psi(1S) ^K+ ^K- pi+ pi-' )

recoK = RCTRUTH ( K , self.mcTruth() )

##  get all MC-particles that makes contribution to recontructed Kaon
mc = self.mcselect ( 'mc' , recoK ) 
print mc 
```
Note that the list of found MC-particles here could be rather long (do you remember _the basic question_?)
{% enddiscussion %}

{% challenge "Challenge" %}
Try to code some MC-truth match algorithm, that get some MC-decays, some reconstructed decays, and perform MC-truth macth between them. Try to select as an example the _ordinary_ `ALLSTREAMS.DST`  (not Turbo version!). Processing of `ALLSTREAMS.DST/Turbo` and  various kinds of `ALLSTREAMS.MDST` and `MC-Turbo` requires a bit different configurtaion steos, that we'll discuss later. For a time being y ou can use e.g. the MC-file `'/lhcb/MC/2012/ALLSTREAMS.DST/00033494/0000/00033494_00000013_1.allstreams.dst'`, that contains the true MC-decays  _Bs -> J/psi K+ K- pi+ pi-_ with many intermediate resonances. 
{% solution "Solution" %}
The complete module, that processes the events of `[B_s0 ==> J/psi(1S) K+ K- pi+ pi-]CC` with very rich structure of intermediate resonances is available [here](https://gist.github.com/VanyaBelyaev/0f831ee3a997d005e7b9f490ee200409)
{% endchallenge %}
