# The Bender tutorials [![Build Status](https://travis-ci.org/lhcb/bender-tutorials.svg?branch=master)](https://travis-ci.org/lhcb/bender-tutorials)

These are tutorials for Bender application: 
["User-friently python analysis environment for LHCb"](http://lhcbdoc.web.cern.ch/lhcbdoc/bender).

It is the first attempt to convert existing [TWiki-based tutorials](https://twiki.cern.ch/twiki/bin/view/LHCb/BenderTutorial)
to GitHub platform, inspired  by the great success of [LHCb StarterKit lessons](https://lhcb.github.io/starterkit-lessons).


Bender is [LHCb Python-based Physics Analysis Environment](http://lhcbdoc.web.cern.ch/lhcbdoc/bender).
It combines the physics content of [DaVinci-project](http://cern.ch/LHCb-release-area/DOC/davinci)
with the interactive python abilities provided by [GaudiPython](http://cern.ch/twiki/bin/view/LHCb/GaudiPython).
It also could be considered as ["Interactive LoKi"](http://cern.ch/lhcb-comp/Analysis/Loki).
The major functionality comes from ROOT/Reflex dictionaries for the basic C++ classes  and the interfaces.  
These dictionaries are used primary for POOL persistency and 
effectively reused for interactivity.  The main purpose of top-level scripts is the coherent 
orchestration of the Reflex dictionaries and the proper decoration of the available interfaces. 


Bender dependencies are   sketched here:[](http://lhcb-doxygen.web.cern.ch/lhcb-doxygen/bender/latest/dependencies.svg) 


Doxigen documentation for Bender is accessible [here](http://lhcb-doxygen.web.cern.ch/lhcb-doxygen/bender/latest/index.html).


It is assumed that users are already has some knowledge of LCHb software, in particular DaVinci and 
are familiar with [LHCb Starterkit][starterkit].


You can also add relative links within the website like this one to the [first section](getting-started)!

[starterkit]: https://lhcb.github.io/starterkit
[first-analysis-steps]: https://lhcb.github.io/starterkit-lessons/first-analysis-steps/
