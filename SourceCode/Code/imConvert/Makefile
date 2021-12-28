CXX = g++
CUDAROOT  = /usr/local/cuda
CUDALIB   = $(CUDAROOT)/lib64
CUDAINC   = $(CUDAROOT)/include
CCFLAGS   =  -Iinclude -DGTE_USE_LINUX -DDEBUG -O0 
NVCC = $(CUDAROOT)/bin/nvcc
INCLUDES = -I. -Iinclude -I../shared -I../shared/include -I../shared/SplineGenerate -I../shared/FastAC -I../shared/squash -I../shared/squash/squash -Wl,-rpath=../shared/squash/squash -L../shared/squash/squash -L$(CUDALIB) -I$(CUDAINC)
CPPFLAGS = -Wall -g -fPIC -Wextra -pedantic -fopenmp 
CPPFLAGS += $(INCLUDES)
CPPFLAGS += $(CCFLAGS)
CUDAFLAGS = -lcuda -lcudart

MPATH=$(realpath $(lastword $(MAKEFILE_LIST)))
NOTHING = 
SPACE = $(NOTHING) $(NOTHING)

OUTDIR = out/
OUTFILE = skeletonify

SOURCE = Image.cpp ImageEncoder.cpp ImageWriter.cpp main.cpp skeleton_cuda.cpp
OBJ = $(OUTDIR)$(subst $(SPACE), $(OUTDIR),$(SOURCE:.cpp=.o))

LFLAG=-L../shared/SplineGenerate/lib/configuration -lgtapplications -lgtgraphics -lgtmathematicsgpu -lX11 -lXext -lGL -lEGL -lpng -lpthread -lm 

EXTERNAL = ../shared/bcl/libbcl.a ../shared/FastAC/arithmetic_codec.o  ../shared/CUDASkel2D/CUDASkel2D.a ../shared/configParser/lib/Config.a ../shared/squash/squash/libsquash0.8.so ../shared/SplineGenerate/BSplineCurveFitter/configuration/BSplineCurveFitterWindow3.o 
ABSOUT=$(MPATH:Makefile=)$(OUTDIR)
.PHONY: all out
default:all


all: ../shared/FastAC/arithmetic_codec.o ../shared/squash/squash/libsquash0.8.so ../shared/CUDASkel2D/CUDASkel2D.a ../shared/SplineGenerate/BSplineCurveFitter/configuration/BSplineCurveFitterWindow3.o $(OBJ) 
	$(CXX) $(CPPFLAGS) -o $(OUTFILE) $(OBJ) $(EXTERNAL) $(CUDAFLAGS) $(LFLAG)

../shared/CUDASkel2D/CUDASkel2D.a:
	make -C ../shared/CUDASkel2D -f Makefile skeleton

../shared/squash/Makefile:
	(cd ../shared/squash;./configure)

../shared/squash/squash/libsquash0.8.so: ../shared/squash/Makefile
	make -C ../shared/squash -f Makefile

../shared/bcl/libbcl.a:
	make -C ../shared/bcl -f Makefile 

../shared/configParser/lib/Config.a:
	make -C ../shared/configParser -f Makefile

../shared/SplineGenerate/BSplineCurveFitter/configuration/BSplineCurveFitterWindow3.o:
	make -C ../shared/SplineGenerate/BSplineCurveFitter CFG=configuration APP=BSplineCurveFitter -f ../makesample.gte 

../shared/FastAC/arithmetic_codec.o:
	g++ -g ../shared/FastAC/arithmetic_codec.cpp -c -o ../shared/FastAC/arithmetic_codec.o



library : all
	@mkdir -p lib
	@ar rvs lib/imconvert.a $(OUTDIR)*.o


$(OUTDIR)%.o : %.cpp
	@mkdir -p $(OUTDIR)
	$(CXX) $(CPPFLAGS) -c $< -o $@

clean:
	$(RM) *.o *.gch ../shared/SplineGenerate/BSplineCurveFitter/configuration/BSplineCurveFitterWindow3.o ../shared/CUDASkel2D/CUDASkel2D.a
	$(RM) ../shared/FastAC/arithmetic_codec.o
	$(RM) -rf lib
	$(RM) -rf out
	$(RM) $(OUTFILE)

clean-lib: clean
	$(RM) -rf lib/

clean-full: clean clean-lib
	make -C ../shared/CUDASkel2D -f Makefile clean
	make -C ../shared/squash -f Makefile clean
	make -C ../shared/configParser -f Makefile clean
	make -C ../shared/bcl -f Makefile clean

