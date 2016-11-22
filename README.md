# Welcome to the IsoAdvector project

## What is IsoAdvector?

IsoAdvector is a geometric Volume-of-Fluid method for advection of a sharp 
interface between two incompressible fluids. It works on both structured and 
unstructured meshes with no requirements on cell shapes. IsoAdvector is meant as 
a replacement for the interface compression with the MULES limiter implemented 
in the interFoam family of solvers.

The isoAdvector concept and code was developed at DHI and was funded by a Sapere
Aude postdoc grant to Johan Roenby from The Danish Council for Independent
Research | Technology and Production Sciences (Grant-ID: DFF - 1337-00118B - FTP).
Co-funding is also provided by the GTS grant to DHI from the Danish Agency for
Science, Technology and Innovation.

The ideas behind and performance of the isoAdvector scheme is documented in:

Roenby J, Bredmose H, Jasak H. 2016 A computational method for sharp interface 
advection. R. Soc. open sci. 3: 160405. [http://dx.doi.org/10.1098/rsos.160405](http://dx.doi.org/10.1098/rsos.160405)

Videos showing isoAdvector's performance with a number of standard test cases 
can be found in this youtube channel:

https://www.youtube.com/channel/UCt6Idpv4C8TTgz1iUX0prAA

## Requirements:

The isoAdvector code is currently compatible with OpenFOAM-2.2.0, OpenFOAM 4.0 
and foam-extend-3.2. The code should also compile with other foam versions with
only minor modifications.

## Installation:

0.  Source a supported OpenFOAM environment: 

        source OpenFOAM-x.x.x/etc/bashrc

1.  In a linux terminal download the package with git by typing:

        git clone https://github.com/isoAdvector/isoAdvector.git

2.  Execute the Allwmake script by typing (will finish in a ~1 min):

        cd isoadvector
        ./Allwmake

    Applications will be compiled to your FOAM_USER_APPBIN and libraries will be
    compiled to your FOAM_USER_LIBBIN.
    
3.  Test installation with a simple test case by typing (finishes in secs):

        cp -r run/isoAdvector/discInUniFlow/baseCase ~
        cd ~/baseCase
        ./Allrun
	
    Open Paraview and color the mesh by the alpha.water/alpha1 field.

4.  (Optional) If you want to test the code on unstructured meshes, a number of 
    such meshes can be donwloaded by using the downloadMeshes script in the bin 
    directory. These meshes will take up ~2GB and will be placed in a folder 
    called meshes. The meshes will be loaded by the scripts in discInUniFlows, 
    vortexSmearedDisc, sphereInUniFlow and smearedSphere cases in 
    run/isoAdvector.

    Alternatively, the meshes can be downloaded directly from here:

        http://dx.doi.org/10.5061/dryad.66840

    The downloaded meshes.tar.gz file should be extracted to the isoadvector 
    root directory.
    
## Code structure:

`src/` 

* Contains the three classes implement the IsoAdvector advection scheme:
    - `isoCutFace`
    - `isoCutCell` 
    - `isoAdvection`
  These are compiled into a library named `libIsoAdvection`. 
* For comparison we also include the CICSAM, HRIC and mHRIC algebraic VOF 
  schemes in `finiteVolume` directory. These are compiled into a library called
  libVOFInterpolationSchemes.

`applications/` 

- `solvers/isoAdvector` 
    - Solves the volume fraction advection equation in either steady flow or 
      periodic predefined flow with the option of changing the flow direction at
      a specified time.
- `solvers/mulesFoam`
    - This is like isoAdvector, but using MULES instead of isoAdvector. Based on
      interFoam.
- `solvers/passiveAdvectionFoam` 
    - This is essentially scalarTransportFoam but using alpha1 instead of T and 
      without diffusion term. Used to run test cases with predefined velocity 
      field using the CICSAM and HRIC schemes.
- `solvers/interFlow` 
    - This is essentially the original interFoam solver with MULES replaced by 
      isoAdvector for interface advection. No changes to PIMPLE loop.
- `utilities/preProcessing/isoSurf` 
    - Sets the initial volume fraction field for either a sphere, a cylinder or 
      a plane. See isoSurfDict for usage.
- `utilities/postProcessing/uniFlowErrors`
    - For cases with spheres and discs in steady uniform flow calculates errors 
      relative to exact VOF solution.
- `test/isoCutTester`
    - Application for testing isoCutFace and isoCutCell classes.

`run/`

- `isoAdvector/` 
    - Contains test cases using isoAdvector to move a volume of fluid in a 
      predescribed velocity field.
- `interFlow/` 
    - Contains test cases using interFlow coupling IsoAdvector with the PIMPLE 
      algorithm for the pressure-velocity coupling.
- `isoCutTester/`
    - Case for testing the isoCutFace and isoCutCell classes.
      
## Classes
	
###  IsoAdvection 

- Calculates the total volume of water, dVf, crossing each face in the mesh 
  during the time interval from time t to time t + dt.

### IsoCutCell

- Performs cutting of a cell given an isovalue and the alpha values interpolated 
  to the cell vertices.
- Calculates the submerged volume of a cell for a given isovalue.
- Calculates the isovalue given a specified target volume fraction of a cell.
  
### IsoCutFace

- Performs cutting of a face given an isovalue and alpha values interpolated to
  the face vertices.
- Calculates the submerged face area given an isovalue and alpha values 
  interpolated to the face vertices.

## Contributors:

* Johan Roenby <jro@dhigroup.com> (Inventor and main developer)
* Hrvoje Jasak <hrvoje.jasak@fsb.hr> (Consistent treatment of boundary faces 
  including processor boundaries, parallelisation, code clean up)
* Henrik Bredmose <hbre@dtu.dk> (Assisted in the conceptual development)
* Vuko Vukcevic <vuko.vukcevic@fsb.hr> (Code review, profiling, porting to 
  foam-extend, bug fixing, testing)
* Tomislav Maric <tomislav@sourceflux.de> (Source file rearrangement)
