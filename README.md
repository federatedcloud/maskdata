# maskdata

This utility will write out a masked dynamic spectra.
It was originally based on PRESTO's `prepdata`, but modified to write out the masked dynamic spectra and stop.

------------------

## Summary

The maskdata task is a copy of PRESTO's `prepdata` with one key addition.  It writes 
a headerless SIGPROC filterbank file of the raw pulsar data with the 
rfi mask applied.  This can then be used later in single pulse search
or other searches. 

## Changes to PRESTO

New:  

   * `$PRESTO/src/mask_data.c` 

Modified:  

   * `$PRESTO/src/backend_common.c`
   * `$PRESTO/include/backend_common.h`
   * `$PRESTO/src/Makefile` 


### `mask_data.c`

This is a copy of prepdata.c with all function calls to "read_psrdata()" 
changed to "read_psrdata_mask()".  The definitions of these two functions 
are in backend_common.c.


### `backend_common.c`

This contains the functions used for reading in pulsar data.  The main 
change we make is to add "read_psrdata_mask()".  This function is a 
copy of "read_psrdata()", but with a write step that writes the masked 
data to file.  The only changes are for opening and writing this file. 
The file name is hard-coded as "raw_data_with_mask.fil".  


### `backend_common.h`

Added "read_psrdata_mask" to the header file.

### `Makefile`

Needed to add a maskdata target in the Makefile.  Also needed to 
add to list of binaries.  

