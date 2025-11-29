# Bohai-Fracture-DEM
Input files and scripts for DEM/PFC modeling of fracture effectiveness in buried-hill reservoirs.


Program name:
PFC2D Darcy Flow and Hydraulic Fracture Propagation in Buried-Hill Reservoirs

Manuscript title:
Constraint effect of natural fracture effectiveness on hydraulic fracture propagation
in buried hill reservoirs of the Bohai Sea

Authors:
Kang Yan a,*
Denglin Han a,**
Chenchen Wang a
Lijuan Wang a
Hao Du a
Chaobin Zhu a
Binyu Ma a

Affiliation:
a Yangtze University, School of Geosciences, Wuhan, Hubei, China

* First author. E-mail address: 2022730043@yangtzeu.edu.cn (K. Yan)
** Corresponding author. E-mail address: handl@yangtzeu.edu.cn (D. Han)

Contact E-mail addresses:
2022730043@yangtzeu.edu.cn (K. Yan)
handl@yangtzeu.edu.cn (D. Han)
wcc1220@163.com (C. Wang)
2629434067@qq.com (L. Wang)
duhao11@hotmail.com (H. Du)
zhuchaobin2022@163.com (C. Zhu)
mabinyu@yangtzeu.edu.cn (B. Ma)

------------------------------------------------------------
1. Purpose of the program
------------------------------------------------------------

This program package contains a set of PFC2D scripts used to simulate hydraulic fracture
propagation in metamorphic buried-hill reservoirs with different natural fracture
effectiveness.

The simulations support the manuscript
“Constraint effect of natural fracture effectiveness on hydraulic fracture propagation
in buried hill reservoirs of the Bohai Sea”
by reproducing the mechanical response and fracture evolution of three discrete element
models:

- Type A: fully filled natural fractures
- Type B: partially filled natural fractures
- Type C: unfilled natural fractures

The scripts combine several contact/bond models (LCM, SJM, FJM, PBM) to represent
heterogeneous mechanical connections among pores, fillings and matrix, and implement a
coupled Darcy-type flow formulation with fracture statistics.

------------------------------------------------------------
2. Software and system requirements
------------------------------------------------------------

- Discrete element code:
  PFC2D version 5.00.40 (Itasca Consulting Group, Inc.)

- Operating system:
  Microsoft Windows (tested under Windows 10 x64)

- Hardware:
  Standard workstation; no special hardware is required for the provided small test case.

------------------------------------------------------------
3. Package contents
------------------------------------------------------------

The compressed archive contains the following main items:

(1) Documentation
- PFC2D_Darcy_Fracture_User_Manual_and_Code_Documentation.pdf
  User manual and code documentation describing the model setup, script structure,
  input/output files, and instructions for running the simulations.

(2) Source code (PFC2D scripts) – folder "code/"
- code/DOALL.p2dat      – master control script
- code/01-SET.p2dat     – geometry and particle generation
- code/02-Contact.p2dat – contact models and micro-parameters
- code/03-Rongshi.p2dat – rock-state saving
- code/04-Dakong.p2dat  – cavity / wellbore creation
- code/05-Daxi.p2dat    – Darcy flow loading and coupled simulation
- code/crack_num.p2dat  – fracture (bond-break) statistics
- code/fracture.p2dat   – floater elimination and packing stabilization
- code/dom.p2dat        – flow-domain (region) identification
- code/dom1.p2dat       – pressure update and volume balance

(3) Test data – folder "test_data/"
- Small input/model files needed to run a compact test case that demonstrates the
  workflow described in the manuscript.

(4) Test output – folder "test_output/"
- Example output files generated from the test data, including:
  - Selected PFC2D history exports (e.g., time vs. pressure, flow rate, fracture count)
  - Final model save(s), if applicable
  These files allow users to verify that their run reproduces the same or very similar
  results.

No executable (.exe) files are provided, in accordance with the Computers & Geosciences
guidelines.

------------------------------------------------------------
4. Quick-start instructions (test case)
------------------------------------------------------------

1) Copy all files and folders from the archive into a working directory accessible
   to PFC2D.

2) Start PFC2D (version 5.00.40) and set the working directory to this location.

3) In the PFC2D command line, run the master script to build the base model and cavity:

   call code/DOALL.p2dat

   This generates the bonded-rock sample with the drilled cavity (wellbore) as used for
   the Darcy-flow stage.

4) Then run the Darcy-flow simulation script:

   call code/05-Daxi.p2dat

   This script:
   - Restores the cavity model
   - Applies flow boundary conditions
   - Calls the domain and pressure-update routines
   - Tracks fracture (bond-break) statistics via crack_num.p2dat
   - Records histories such as time, flow-related variables, and total fracture count

5) After the simulation finishes, export histories and compare them to the reference
   files in "test_output/" to check that the program runs correctly.

More detailed step-by-step instructions are provided in:
PFC2D_Darcy_Fracture_User_Manual_and_Code_Documentation.pdf

------------------------------------------------------------
5. Notes and limitations
------------------------------------------------------------

- The provided test data set is intentionally small to avoid download issues and to
  allow reasonably fast runs on standard hardware.

- For full-scale production runs or alternative parameter sets (e.g., different
  confining pressures or fracture effectiveness types), the user may adapt the
  micro-parameters in 02-Contact.p2dat and the flow-related parameters in 05-Daxi.p2dat,
  dom.p2dat and dom1.p2dat.

- Any modifications used to generate new results should be documented by the user when
  reproducing or extending the study.

------------------------------------------------------------
6. Contact
------------------------------------------------------------

Questions regarding the program code or its usage can be addressed to:

First author:
Kang Yan
Yangtze University, School of Geosciences, Wuhan, Hubei, China
E-mail: 2022730043@yangtzeu.edu.cn

Corresponding author:
Denglin Han
Yangtze University, School of Geosciences, Wuhan, Hubei, China
E-mail: handl@yangtzeu.edu.cn
