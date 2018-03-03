# Advanced fill of n-tuples 

The n-tuple filling functionality, described [above](getting-started/firstalgorithms.md) is 
drasticlaly extended using the functions from `BenderTools.Fill` module.
The import of this module  add following functions to the base class `Algo`:

|  Method           |  Short description   | 
| ---               |  ---                 | 
| `treatPions`      | add information about pions    |
| `treatKaons`      | add information about kaons    | 
| `treatProtons`    | add information about protons  | 
| `treatMuons`      | add information about muons    | 
| `treatPhotons`    | add information about photons  | 
| `treatDiGammas`   | add information about di-photons (pi0, eta,...)  | 
| `treatTracks`     | add information about the tracks   | 
| `treatKine`       | add detailed kinematic information for the particle  | 
| `fillMasses`      | masses of sub-combinations   | 
| `addRecSummary`   | add rec-summary information   | 
| `addGecInfo`      | add some GEC-info  | 

These methods can be considered as a kind of  very light _tuple-tools_.
All of them are (well) documented and one can easily inspect them:
```python
import BenderTools.Fill
from Bender.Main import Algo
help(Algo.treatPions)
```
Also all these methods  print  detailed how-to infomratino  in log-file  at the moment of the first invoke, and it vasn be very helpful to understand the branches in n-tuple/tree, e.g.
```python
# BenderTools.Fill          INFO    treatTracks: The method adds track-specific information into n-tuple
#     ...
#     tup = ... ## n-tuple 
#     b   = ... ## the particle  (or looping object)
#     self.treatTracks ( tup , b , '_B' ) ## suffix is optional 
#     ...                
#     Following variables are added into n-tuple:
#     - deltaM2_min_track_ss/os[+suffix]:
#     Minimal value of delta_m2(track1, track2) for all pairs of same-sign (``_ss'')
#     and opposite sign ``_os'' tracks, where function minm2 is
#     delta_M2(p1,p2) = (m^2(p1+p2) - 2*m^2(p1)-2*m^2(p2) )/m^2(p1+p2)
#     see  LoKi::Kinematics::deltaM2
#     - deltaAlpha_min_track_ss/os[+suffix]:
#     Minimal value of the angle between two momenta for all pairs of same-sign (``_ss'')
#     and opposite sign ``_os'' tracks
#     see  LoKi::Kinematics::deltaAlpha
#     - overlap_max_track_ss/os[+suffix]:
#     Maximal value ``overlap'' for all pairs of same-sign (``_ss'')
#     and opposite sign ``_os'' tracks
#     ``Overlap'' is defined as fraction of common/shared hits between two tracks 
#     see LHCb::HasIDs::overlap 
#     - minPt_track[+suffix]
#     Minimal pT of the tracks
#     - min/maxEta_track[+suffix]
#     Minimal/maximal eta/pseudorapidity of the tracks
#     - maxChi2_track[+suffix]
#     Maximal chi2/ndf for the track
#     - minKL_track[+suffix]
#     Minimal value of Kullback-Leibler divergency for the tracks
#     - maxTrGh_track[+suffix]
#     Maximal value of Track Ghost probability for the tracks (track-based)
#     - maxAnnGh_track[+suffix]
#     Maximal value of       Ghost probability for the tracks (PID-based)
#     - n_track[+suffix] 
#     Number of tracks in the decay
#     
#     And then for each track in the decay:
#     - p_track[+suffux]     momentum of the track
#     - pt_track[+suffux]    transverse momentum of the track
#     - eta_track[+suffux]   eta/pseudorapidity  of the track
#     - phi_track[+suffux]   phi (azimuth angle) of the track
#     - chi2_track[+suffux]  chi2/ndf of the track
#     - PChi2_track[+suffux] fit probability calculated from chi2/ndf of the track
#     - ann_track[+suffix]   Ghost probability (PID-based)
#     - trgh_track[+suffix]  Track Ghost probability (Track-based)
```


The typical usage of these methods is: 
```python
tup = self.nTuple('MyTuple')
for p in particles :
            
    psi = p(1) ## the first daughter: J/psi 
            
    ## fill few kinematic variables for the particles:
    self.treatKine   ( tup , p   , '_b'   )  ## use the suffix to mark variables 
    self.treatKine   ( tup , psi , '_psi' )  ## use the suffix to mark variables 
            
    self.treatKaons  ( tup , p ) ## fill some basic information for all kaons
    self.treatMuons  ( tup , p ) ## fill some basic information for all muons
    self.treatTracks ( tup , p ) ## fill some basic information for all charged tracks

    tup.write()     
```
{% challenge "Challenge" %}
  1. Add (some of) these functions into your previous Bender module with n-tuples.
  2. Run it and observe the detailed  printout in log-file 
  3. Observe new variables in your n-tuple/tree and find their description in the log-file or via `help(Algo.<THEMETHOD>)`
     * Is the description for all new varibales clear enough? 
{% solution "Solution" %}
The complete module is accessible [here](https://gist.github.com/VanyaBelyaev/becc26fe5dea90aa96cb8f929faf6a53) and the corresponsing log-file is [here](https://gist.github.com/VanyaBelyaev/03dc3bc117940dc8a52f8796cc737985)
{% endchallenge %}



