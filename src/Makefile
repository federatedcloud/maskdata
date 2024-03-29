#  Makefile for maskdata (based on PRESTO)

# OS type
OS = Linux

#UNAME = $(shell uname)
#ifeq ($(UNAME),Darwin)
#OS = OSX
#endif

## Linux is the first choice
#ifeq ($(OS),Linux)
#	LIBSUFFIX = .so
#	LIBCMD = -shared
## else assume Darwin (i.e. OSX)
#else
#	LIBSUFFIX = .dylib
#	LIBCMD = -dynamiclib
#endif
LIBSUFFIX = .so
LIBCMD = -shared

BASE = ..

## How to link with some needed libraries of PGPLOT
#X11LINK := $(shell pkg-config --libs x11)
#PNGLINK := $(shell pkg-config --libs libpng)
#
## Include and link information for PGPLOT v5.X (including shared libs!)
## Typically you need to have your PGPLOT_DIR environment variable set
#PGPLOTINC = -I$(PGPLOT_DIR)
#PGPLOTLINK = -L$(PGPLOT_DIR) -lcpgplot -lpgplot $(X11LINK) $(PNGLINK)
#
# Include and link information for the FFTW 3.X single-precision library
FFTINC := $(shell pkg-config --cflags fftw3f)
FFTLINK := $(shell pkg-config --libs fftw3f)

# Include and link information for the GLIB 2.0 library
GLIBINC := $(shell pkg-config --cflags glib-2.0)
GLIBLINK := $(shell pkg-config --libs glib-2.0)

# Include and link information for CFITSIO
CFITSIOINC := $(shell pkg-config --cflags cfitsio)
CFITSIOLINK := $(shell pkg-config --libs cfitsio)

# The standard PRESTO libraries to link into executables
BASELINK = $(CFITSIOLINK) -L$(BASE)/lib -lpresto $(FFTLINK)

CC = gcc
#CC = clang-3.6
FC = gfortran

# Set this to true if you want to use OpenMP.  false otherwise
USEOPENMP = true

## Set this to true if you want to profile.
#USEPROFILE = false

# Very recent Intel CPUs might see a few percent speedup using -mavx
#CFLAGS = -I$(BASE)/include $(GLIBINC) $(CFITSIOINC) $(PGPLOTINC) $(FFTINC) \

CFLAGS = -I$(BASE)/include $(GLIBINC) $(CFITSIOINC) $(FFTINC) \
	-DUSEFFTW -DUSEMMAP -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 \
	-g -Wall -W -fPIC -O3 -ffast-math
CLINKFLAGS = $(CFLAGS)

# Add some GCC-specific flags that are useful
ifeq ($(CC),gcc)
	CFLAGS += -Wno-unused-result -Wno-unused-but-set-variable \
		-Wno-unused-but-set-parameter
# The following is great as long as your GCC is > v 4.9
#	CFLAGS += -fdiagnostics-color=auto
endif

# NOTE:  Be careful of upping the optimization on the
#        FFLAGs.  Certain compilers (i.e. on Intel Macs) will
#        cause errors for the code in least_squares.f
FFLAGS = -g -fPIC
FLINKFLAGS = $(FFLAGS)

# Add flags we need for openmp
ifeq ($(USEOPENMP),true)
	ifeq ($(CC),icc)
		OMPFLAGS = -openmp
		CFLAGS += $(OMPFLAGS)
		FLINKFLAGS += $(OMPFLAGS)
# for GCC and clang
	else
		OMPFLAGS = -fopenmp
		CFLAGS += $(OMPFLAGS)
		FLINKFLAGS += $(OMPFLAGS)
	endif
endif

## Add flags we need for profiling (including making a static libpresto)
#ifeq ($(USEPROFILE),true)
#	CFLAGS += -pg
#	FFLAGS += -pg
#	LIBSUFFIX = .a
#endif

#ifeq ($(LIBSUFFIX),.so)
#	LINKCOMMAND = $(CC) $(LIBCMD) $(OMPFLAGS) $(FFTLINK) -o
#else
#	LINKCOMMAND = ar rcs
#endif
LINKCOMMAND = $(CC) $(LIBCMD) $(OMPFLAGS) $(FFTLINK) -o

## Add to the search path for the executables
#VPATH = ../lib:../bin

# When modifying the CLIG files, the is the location of the clig binary
CLIG = clig

# Rules for CLIG generated files
%_cmd.c : ../clig/%_cmd.cli
	cd ../clig ; $(CLIG) -o $*_cmd -d $<
	mv ../clig/$*_cmd.h ../include/
	mv ../clig/$*_cmd.c .
	cp ../clig/$*.1 ../docs/

OBJS = amoeba.o atwood.o barycenter.o birdzap.o cand_output.o\
	characteristics.o cldj.o chkio.o corr_prep.o corr_routines.o\
	correlations.o database.o dcdflib.o dispersion.o\
	fastffts.o fftcalls.o fminbr.o fold.o fresnl.o ioinf.o\
	get_candidates.o iomak.o ipmpar.o maximize_r.o maximize_rz.o\
	maximize_rzw.o median.o minifft.o misc_utils.o clipping.o\
	orbint.o output.o read_fft.o readpar.o responses.o\
	rzinterp.o rzwinterp.o select.o sorter.o swapendian.o\
	transpose.o twopass.o twopass_real_fwd.o\
	twopass_real_inv.o vectors.o multifiles.o mask.o\
	fitsfile.o hget.o hput.o imio.o djcl.o range_parse.o

#OBJS = barycenter.o dispersion.o ioinf.o chkio.o median.o\
    transpose.o range_parse.o misc_utils.o clipping.o\
    vectors.o mask.o multifiles.o fitsfile.o output.o\
    swapendian.o hget.o hput.o cldj.o minifft.o imio.o\
    characteristics.o fold.o fftcalls.o dcdflib.o birdzap.o\
    ipmpar.o sorter.o responses.o 

INSTRUMENTOBJS = backend_common.o zerodm.o sigproc_fb.o psrfits.o

# Use old header reading stuff for readfile
READFILEOBJS = $(INSTRUMENTOBJS) multibeam.o bpp.o spigot.o \
	wapp.o wapp_head_parse.o wapp_y.tab.o

PLOT2DOBJS = powerplot.o xyline.o

#BINARIES = makedata makeinf mjd2cal realfft quicklook\
	search_bin swap_endian prepdata maskdata\
	check_parkes_raw bary shiftdata dftfold\
	patchdata readfile toas2dat taperaw\
	accelsearch prepsubband cal2mjd split_parkes_beams\
	dat2sdat sdat2dat downsample rednoise un_sc_td bincand\
	psrorbit window plotbincand prepfold show_pfd\
	rfifind zapbirds explorefft exploredat\
	weight_psrfits fitsdelrow fitsdelcol psrfits_dumparrays

BINARIES = readfile makeinf maskdata

#all: libpresto slalib binaries
all: libpresto binaries

# Default indentation is K&R style with no-tabs,
# an indentation level of 4 (default), and a line-length of 85
indent:
	indent -kr -nut -l85 *.c
	rm *.c~

prep:
	touch *_cmd.c

#makewisdom:
#	$(CC) $(CLINKFLAGS) -o $@ makewisdom.c $(FFTLINK)
#	./makewisdom
#	cp fftw_wisdom.txt $(BASE)/lib

timetest:
	$(CC) -o $@ timetest.c
	./timetest
	rm -f timetest

libpresto: libpresto$(LIBSUFFIX)

libpresto$(LIBSUFFIX): $(OBJS)
	$(LINKCOMMAND) $(BASE)/lib/$@ $(OBJS)

#slalib: libsla$(LIBSUFFIX)
#	cd slalib ; $(FC) -o sla_test sla_test.f -fno-second-underscore -L$(BASE)/lib -lsla
#	slalib/sla_test
#
#libsla$(LIBSUFFIX):
#	cd slalib ; $(FC) $(FFLAGS) -fno-second-underscore -c -I. *.f *.F
#	rm slalib/sla_test.o
#	cd slalib ; $(FC) $(LIBCMD) -o $(BASE)/lib/libsla$(LIBSUFFIX) -fno-second-underscore *.o

binaries: $(BINARIES)

#mpi: mpiprepsubband
#
#mpiprepsubband_utils.o: mpiprepsubband_utils.c
#	mpicc $(CLINKFLAGS) -c mpiprepsubband_utils.c
#
#mpiprepsubband.o: mpiprepsubband.c
#	mpicc $(CLINKFLAGS) -c mpiprepsubband.c
#
#mpiprepsubband: mpiprepsubband_cmd.c mpiprepsubband_cmd.o mpiprepsubband_utils.o mpiprepsubband.o $(INSTRUMENTOBJS) libpresto
#	mpicc $(CLINKFLAGS) -o $(BASE)/bin/$@ mpiprepsubband_cmd.o mpiprepsubband_utils.o mpiprepsubband.o $(INSTRUMENTOBJS) $(BASELINK) -lcfitsio -lm

accelsearch: accelsearch_cmd.c accelsearch_cmd.o accel_utils.o accelsearch.o zapping.o libpresto
	$(CC) $(CLINKFLAGS) $(OMPFLAGS) -o $(BASE)/bin/$@ accelsearch_cmd.o accel_utils.o accelsearch.o zapping.o $(BASELINK) $(GLIBLINK) -lm

bary: bary.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ bary.o $(BASELINK) -lm

bincand: bincand_cmd.c bincand_cmd.o bincand.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ bincand.o bincand_cmd.o $(BASELINK) -lm

dftfold: dftfold_cmd.c dftfold_cmd.o dftfold.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ dftfold.o dftfold_cmd.o $(BASELINK) -lm

shiftdata: shiftdata.o
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ shiftdata.o -lm

patchdata: patchdata.o
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ patchdata.o

dat2sdat: dat2sdat.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ dat2sdat.o $(BASELINK) -lm

sdat2dat: sdat2dat.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ sdat2dat.o $(BASELINK) -lm

check_parkes_raw: check_parkes_raw.o multibeam.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ check_parkes_raw.o multibeam.o $(BASELINK) -lm

downsample: downsample_cmd.c downsample.o downsample_cmd.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ downsample.o downsample_cmd.o $(BASELINK) -lm

split_parkes_beams: split_parkes_beams.o
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ split_parkes_beams.o

test_multifiles: test_multifiles.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ test_multifiles.o $(BASELINK) -lm

rfifind: rfifind_cmd.c rfifind_cmd.o rfifind.o rfi_utils.o rfifind_plot.o $(INSTRUMENTOBJS) $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@  $(INSTRUMENTOBJS) $(PLOT2DOBJS) rfifind.o rfi_utils.o rfifind_cmd.o rfifind_plot.o $(BASELINK) $(PGPLOTLINK) -lcfitsio -lm

prepdata: prepdata_cmd.c prepdata_cmd.o prepdata.o $(INSTRUMENTOBJS) libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)$@ prepdata.o prepdata_cmd.o $(INSTRUMENTOBJS) $(BASELINK) -lcfitsio -lm

prepsubband: prepsubband_cmd.c prepsubband_cmd.o prepsubband.o $(INSTRUMENTOBJS) libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ prepsubband.o prepsubband_cmd.o $(INSTRUMENTOBJS) $(BASELINK) -lcfitsio -lm

prepfold: prepfold_cmd.c prepfold_cmd.o prepfold.o prepfold_utils.o prepfold_plot.o least_squares.o polycos.o readpar.o $(INSTRUMENTOBJS) $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)$@ prepfold.o prepfold_utils.o prepfold_plot.o prepfold_cmd.o least_squares.o polycos.o readpar.o $(PLOT2DOBJS) $(INSTRUMENTOBJS) $(LAPACKLINK) $(BASELINK) $(PGPLOTLINK) -lcfitsio -lm

maskdata: prepfold_cmd.c prepdata_cmd.o mask_data.o $(INSTRUMENTOBJS) libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)$@ mask_data.o prepdata_cmd.o $(INSTRUMENTOBJS) $(BASELINK) -lcfitsio -lm

dump_spigot_zerolag: dump_spigot_zerolag.o spigot.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ dump_spigot_zerolag.o spigot.o $(BASELINK) -lm

spigot2filterbank: spigot2filterbank_cmd.c spigot2filterbank_cmd.o spigot2filterbank.o spigot.o sigproc_fb.o sla.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ spigot2filterbank.o spigot.o sigproc_fb.o spigot2filterbank_cmd.o sla.o $(BASELINK) -lsla -lm

GBT350filterbank: GBT350filterbank.o spigot.o sigproc_fb.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ GBT350filterbank.o spigot.o sigproc_fb.o $(BASELINK) -lm

spigotSband2filterbank: spigotSband2filterbank.o spigot.o sigproc_fb.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ spigotSband2filterbank.o spigot.o sigproc_fb.o $(BASELINK) -lm

show_pfd: show_pfd_cmd.c show_pfd.o show_pfd_cmd.o prepfold_utils.o prepfold_plot.o least_squares.o $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@ show_pfd.o show_pfd_cmd.o prepfold_utils.o prepfold_plot.o least_squares.o $(PLOT2DOBJS) $(LAPACKLINK) $(BASELINK) $(PGPLOTLINK) -lm

makedata: com.o randlib.o makedata.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ com.o randlib.o makedata.o $(BASELINK) -lm

makeinf: makeinf.o ioinf.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)$@ makeinf.o ioinf.o $(BASELINK) -lm

mjd2cal: djcl.o mjd2cal.o
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ djcl.o mjd2cal.o -lm

cal2mjd: cldj.o cal2mjd.o
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ cldj.o cal2mjd.o -lm

plotbincand: plotbincand.o $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@ $(PLOT2DOBJS) plotbincand.o $(BASELINK) $(PGPLOTLINK) -lm

profile: profile_cmd.c profile_cmd.o profile.o $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@ $(PLOT2DOBJS) profile.o profile_cmd.o $(BASELINK) $(PGPLOTLINK) -lm

psrorbit: psrorbit.o $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@ $(PLOT2DOBJS) psrorbit.o $(BASELINK) $(PGPLOTLINK) -lm

testbinresp: testbinresp.o $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@ testbinresp.o $(PLOT2DOBJS) $(PGPLOTLINK) $(BASELINK) -lm

quicklook: quicklook.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ quicklook.o $(BASELINK) -lm

readfile: readfile_cmd.c readfile_cmd.o readfile.o $(READFILEOBJS) libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)$@ readfile.o readfile_cmd.o $(READFILEOBJS) $(BASELINK) -lcfitsio -lm

realfft: realfft_cmd.c realfft_cmd.o realfft.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ realfft.o realfft_cmd.o $(BASELINK) -lm

rednoise: rednoise_cmd.c rednoise.o rednoise_cmd.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ rednoise.o rednoise_cmd.o $(BASELINK) -lm

search_bin: search_bin_cmd.c search_bin_cmd.o search_bin.o libpresto
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ search_bin.o search_bin_cmd.o $(BASELINK) -lm

taperaw: taperaw.o
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ taperaw.o

toas2dat: toas2dat_cmd.c toas2dat_cmd.o toas2dat.o
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ toas2dat.o toas2dat_cmd.o

un_sc_td:
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ un_sc_td.c

swap_endian:
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ swap_endian.c

window: window.o $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@ $(PLOT2DOBJS) window.o $(BASELINK) $(PGPLOTLINK) -lm

zapbirds: zapbirds_cmd.c zapbirds_cmd.o zapbirds.o zapping.o $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@ zapbirds_cmd.o zapbirds.o zapping.o $(PLOT2DOBJS) $(BASELINK) $(PGPLOTLINK) $(GLIBLINK) -lm

explorefft: explorefft.o $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@ explorefft.o $(PLOT2DOBJS) $(BASELINK) $(PGPLOTLINK) -lm

exploredat: exploredat.o $(PLOT2DOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@ exploredat.o $(PLOT2DOBJS) $(BASELINK) $(PGPLOTLINK) -lm

weight_psrfits: weight_psrfits.o $(INSTRUMENTOBJS) libpresto
	$(FC) $(FLINKFLAGS) -o $(BASE)/bin/$@ weight_psrfits.o $(INSTRUMENTOBJS) $(BASELINK)

psrfits_dumparrays: psrfits_dumparrays.o
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ psrfits_dumparrays.o $(CFITSIOLINK) -lm

fitsdelrow: fitsdelrow.o
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ fitsdelrow.o $(CFITSIOLINK) -lm

fitsdelcol: fitsdelcol.o
	$(CC) $(CLINKFLAGS) -o $(BASE)/bin/$@ fitsdelcol.o $(CFITSIOLINK) -lm

clean:
	rm -f *.o *~ *#
#	rm -f slalib/*.o slalib/sla_test

cleaner: clean
	cd ../bin ; rm -f $(BINARIES)
	rm -f $(BASE)/lib/libpresto.* $(BASE)/lib/libsla.*

squeaky:  cleaner
	rm -f *.dat *.fft *.inf fftw_wisdom.txt
	rm -f core *.win* *.ps *_rzw *.tmp
	cd $(BASE)/clig ; rm -f *# *~
	cd $(BASE)/docs ; rm -f *# *~
	cd $(BASE)/python ; rm -f *# *~ *.o *.pyc *.pyo
	cd $(BASE)/include ; rm -f *# *~
