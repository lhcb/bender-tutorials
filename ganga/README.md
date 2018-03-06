# Bender & Ganga/GRID

## `BenderModule` in  `Ganga`

To submit the GRID job in `Ganga` with your module, there is an application `BenderModule` in Ganga, writted by Vladimir Romanovsky.  The usage of this application is rather simple:
```python
job = Job  ( ... )
job.application = BenderModule ( 
   module     =     'the_path/my_module.py' , ## <--- HERE  
   directory  = ...   , ## the  directory where the existing project lives  
   platform   = 'x86_64-slc6-gcc62-opt'
)
```
There us helper function `prepareBender` that allows to prepare the application 
```python
job = Job  ( ... )
job.application = prepareBender ( 
   version    = 'v31r0' , 
   platform   = 'x86_64-slc6-gcc62-opt' , 
   ## path       = '$HOME/cmtuser' ## use this  directory to prepare the project
   use_tmp    = True               ## use  some temporary directory 
   params     = ...  ## optionally feed it with params arguments for configure method
   )
```
For more details consult `help(BenderModule)` and `help(prepareBender)` in `Ganga`


## `BenderRun` in  `Ganga`

`BenderRun` is a dedicated application in `Ganga` to run  [_bender script_](../bedner-script/README.py). The usage is fairly  trivial
```python
j.application = BenderRun    ( scripts   = [ 'the_path/the_module.py'     ] ,
                               imports   = [ 'some_miport_file.py'        ] ,
                               commands  = [ 'ls()' , 'run(10)'  , 'ls()' ] ,
                               arguments = [ ... ] ,
                               directory =  ...  )
```
Again, there is helper function `prepareBenderRun`
j.applictaion =  prepareBenderRun ( 
   version   = 'v30r1' , 
   scripts   = ['the_path/the_script.py' , 'another_script.py' ] , 
   commands  = [ ... ] , 
   arguments = [ ... ] ,
   use_tmp   = True    , 
   ...
)
```
For more details consult `help(BenderRun)` and `help(prepareBenderRun)` in `Ganga`
