# modulation_index

This is code to modify an existing installation of the PALFA pipeline, which is based on searching with PRESTO, to execute Laura Spitler's modulation index methods.

------------------

# Instructions

## Summary

In these notes, we show how to incorporate `maskdata` into `PRESTO`.  This 
is necessary to run Laura Spitler's modulation index single pulse search 
as implemented in the PALFA pipeline.  

The maskdata task is a copy of PRESTO's `prepdata` with one key addition.  It writes 
a headerless SIGPROC filterbank file of the raw pulsar data with the 
rfi mask applied.  This is used later in the single pulse search.  

## Changes

New:  

   * `$PRESTO/src/mask_data.c` 

Modified:  

   * `$PRESTO/src/backend_common.c`
   * `$PRESTO/include/backend_common.h`
   * `$PRESTO/src/Makefile` 

All of these files (and the original files, prefixed with "save_") 
can be found in the `changes` folder.

### `mask_data.c`

This is a copy of prepdata.c with all function calls to "read_psrdata()" 
changed to "read_psrdata_mask()".  The definitions of these two functions 
are in backend_common.c.


### `backend_common.c`

This contains the functions used for reading in pulsar data.  The main 
change we make is to add "read_psrdata_mask()".  This function is a 
copy of "read_psrdata()", but with a write step that writes the masked 
data to file.  The only changes are for opening and writing this file. 
The file name is hard-coded as "raw_data_with_mask.fits".  


### `backend_common.h`

Need to add "read_psrdata_mask" to the header file.

### `Makefile`

Need to add a maskdata target in the Makefile.  Also need to 
add to list of binaries.  

## Calculating the modulation index of single pulse candidates in PALFA data

`mi_src.tar` contains:  

   * `palfa_calc_mi.c`
   * `mock_spec.h`

We shouldn't need any more dependencies, so just build:

` $ gcc -Wall palfa_calc_mi.c -o palfa_mi -lm`


The executable is called "palfa_mi" in the PALFA pipeline (called in the 
PALFA2_presto_search_noaccel.py python script), so we want to make sure 
it has the same name.