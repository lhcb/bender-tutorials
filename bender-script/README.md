# `BenderScript` 

`BenderScript` is a kind of _steroid-enhanced_ and _doped_ `GaudiPython` session. `BenderScript` shares all pros and contras with `GaudiPython`. It definitely provides very simple and efficient way for exploring the data, in particular very simple way for investigation of the content for the input data and TES. One can easily loop over events, data containers, make simple calcualtions, apply large part of LoKi functors, use some part of DaVinci machinery, etc. Howver there is large part of tasks that are very difficult in `BenderScrip`:
 - large part of `DaVinci` _tools_ is not working. It includes all _tools_ and functionality related to e.g. actions with the associated best primary vertex. These manipulations are very complicated or some of them (e.g. PV-refit) is practically impossible to perform in a correct way without proper DaVinci context.
 - Many _tools_ do not work without the significant efforts. The results of _tools_ often are very difficult to save to be used/reused e.g. for subsequent processing (e.g. save manually created particles and vertices to TES and to output file)
 - Some very important and popular  LoKi functors do not operate (mainly due to the issues from the previous item), e.g `BPV*` or `*DV` or `DTF_*` - those are not easy to use from the plain command line. There are some alternatives , but not for all functors.

See also a little bit more  detailed [TWiki-based tutorial](https://twiki.cern.ch/twiki/bin/view/LHCb/BenderScriptTutorial) on `BenderScript` 

## `bender` script

`BenderScript` session is started using the command `bender`. There are many command line options and keys for this script.
{% challenge "Challenge" %}
Invoke this command using `-h` option. Is the displayed help clear enough? 
{% endchallenge %}
Many of the options/keys can be deduced from the input data itself, in particular from the names of centrally produced files.

### Input data 

It is mandatory to supply `bender` session with input data.  There are two  ways to do it 
 1. supply data files as command like parameters
```bash
lb-run Bender/prod bender /lhcb/LHCb/Collision12/PSIX.MDST/00035290/0000/00035290_00000221_1.psix.mdst
```
 2. _import_ the python file with data in a form of Gaudi Configurables, e.g. with `IOHelper().Input = [ ...] `
```bash
lb-run Bender/prod bender -i data.py 
```
where `data.py` is
```python
from GaudiConf import IOHelper
IOHelper().inputFiles([
    'root://eoslhcb.cern.ch//eos/lhcb/grid/prod/lhcb/LHCb/Collision12/PSIX.MDST/00035290/0000/00035290_00000221_1.psix.mdst',
], clear=True)
```
Xml-catalogs can be supplied  either via import from `data.py` or explicitely via `-x`-key
```bash
lb-run Bender/prod bender -i data.py -x xml_catalog.xml 
```
Many of the options/keys (`DataType`, `Simulation`, `Turbo`, 'RootInTES', `InputType` , ... ) can be deduced from the input data itself, in particular from the names of centrally produced files.

### The basic action at command prompt

The incomplete list of the basic commands at the command prompts is :
  - `ls`
  - `get` 
  - `run`
  - `skip`
  - `rewind`

Do not forget that `bender` session is just a python session, therefore one always can invoke the  commands `dir` and `help`. 

{% challenge "Challenge" %}
Start `bender` session and check the description for these method. Is it clear enough? 
{% endchallenge %}

More advanced commands are:
  - `seekForData`
  - `seekStripDecision`
  - `seekForODIN`
  - `seekForEvtRun`
  - `seekForAlgDecision`
  - `seekForVoidDecision`
 
{% challenge "Challenge" %}
Start `bender` session and check the description for these method. Is it clear enough? 
{% endchallenge %}

The functions that help coding the actual scripts:
  - `irun` 
  - `execute` 

### Histograms

Of course one can book & `ROOT` histograms at the command line
```python
h1 = ROOT.TH1D('h1','histo',10,0,1000) 
for i in irun ( ... ) :
     data = get ( .... ) 
     h1.Fill ( len (  data ) 
```
Of course at the end one needs to save this histogram into some ROOT-file

As viable and practical alternative one can rely here on `Gaudi`-histograms:
```python
h1 = book ( 'some_path_here' , 'title' , 100 , 0 , 100 )
...
h1.fill( x ) 
```
In this case all created histograms will be automaticlaly saved into the outout file with historgams, 
that is defined via the command line option `--histofile`, e.g. `bender ... --histofile DVhistos.root`

### N-tuples/Trees

Again, oen can  rely on bare `ROOT` (e.g. `PyROOT`) here to book/fill/save the tuples, 
but one can also use the functionality from `Gaudi`:
```python
t = nTuple('QQQ','MyTuple')
data = ... 
for b in data :
   t.column_float ( 'pt' , PT ( b ) )
   t.column_int   ( 'id' , int ( ID ( b ) ) )
   t.column_float ( 'mass' , M ( b ) )
   t.write()
``` 
In this case all created histograms will be automaticlaly saved into the outout file with historgams, 
that is defined via the command line option `--tuplefile`, e.g. `bender ... --tuplefile DVtuples.root`


 





