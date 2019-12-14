#  Makefile for maskdata (based on PRESTO)

# Assumes Linux
LIBSUFFIX = .so

#LIBCMD = -shared

PRESTOLINK = $(CFITSIOLINK) -L$(PRESTO)/lib -lpresto $(FFTLINK)

CC = gcc

CLINKFLAGS = -I$(PRESTO)/include $(GLIBINC) $(CFITSIOINC) $(PGPLOTINC) $(FFTINC) \
     -DUSEFFTW -DUSEMMAP -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 \
     -g -Wall -W -fPIC -O3 -ffast-math

#OMPFLAGS = -fopenmp
#LINKCOMMAND = $(CC) $(LIBCMD) $(OMPFLAGS) $(FFTLINK) -o
#LINKCOMMAND = $(CC) $(LIBCMD) $(OMPFLAGS) -o

LINKCOMMAND = $(CC) -shared -fopenmp -o




#INSTRUMENTOBJS = backend_common.o zerodm.o sigproc_fb.o psrfits.o
INSTRUMENTOBJS = backend_common.o zerodm.o psrfits.o

libpresto: libpresto$(LIBSUFFIX)

prepdata: prepdata_cmd.c prepdata_cmd.o prepdata.o $(INSTRUMENTOBJS) libpresto
    $(CC) $(CLINKFLAGS) -o ./$@ prepdata.o prepdata_cmd.o $(INSTRUMENTOBJS) $(PRESTOLINK) -lcfitsio -lm

maskdata: prepfold_cmd.c prepdata_cmd.o mask_data.o $(INSTRUMENTOBJS) libpresto
    $(CC) $(CLINKFLAGS) -o ./$@ mask_data.o prepdata_cmd.o $(INSTRUMENTOBJS) $(PRESTOLINK) -lcfitsio -lm







