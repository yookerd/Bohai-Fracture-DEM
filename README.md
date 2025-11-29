# Bohai-Fracture-DEM
Input files and scripts for DEM/PFC modeling of fracture effectiveness in buried-hill reservoirs.

PFC2D Darcy Flow and Fracture Simulation Scripts – Documentation
1. Project Files and Call Structure
This PFC2D project consists of a set of .p2dat scripts for building a bonded-particle rock sample, applying Darcy-type flow, and tracking fracture evolution. The main scripts and their roles are summarized below.
File name	Main function
DOALL.p2dat	Master control script. Calls the geometry, contact, rock-state and cavity scripts in sequence.
01-SET.p2dat	Geometry and particle generation: model size, particle radii, porosity and initial packing.
02-Contact.p2dat	Contact models and micro-parameters: stiffness, strength and friction for different materials.
03-Rongshi.p2dat	Rock-state saving: stores a consistent “base” bonded-rock sample.
04-Dakong.p2dat	Drilling / cavity creation near the sample centre for flow boundary conditions.
05-Daxi.p2dat	Darcy flow loading and calculation: flow boundary, cycling and history recording.
crack_num.p2dat	Fracture statistics: counts bond-break events (tension vs shear, different bond types).
fracture.p2dat	Floater elimination and packing stabilization before flow and fracturing.
dom.p2dat	Domain (flow region) identification and outer-boundary detection.
dom1.p2dat	Pressure-field update and volume balance for coupled flow calculations.
In practice, DOALL.p2dat is first executed to build and save the base model with a central cavity. Then 05-Daxi.p2dat is run to perform the Darcy flow and fracture evolution simulation.
2. Geometry and Particle Generation (01-SET.p2dat)
01-SET.p2dat defines the model dimensions, particle properties and the initial packing.
1.1) Model dimensions and coordinates: A characteristic length L1 = 50 mm is used. The sample width and height are 0.5·L1 (approximately 25 mm × 25 mm). Upper, lower, left and right boundaries are defined accordingly. Ball_porosity, radius_min and radius_max control the initial particle size distribution and target porosity.
2.2) Particle generation and compaction: The particles are generated and compacted using gravity and damping (a_damp) until a dense packing is obtained. The floater-elimination logic in fracture.p2dat is called to remove particles with too few contacts, which improves the mechanical stability of the assembly.
3.3) Particle grouping: ball group commands are used to assign certain regions to specific groups (for example, a central “filling” group), while the remaining particles are treated as the rock matrix. This allows different contact properties or local modifications to be applied in later steps.
3. Contact Models and Micro-Parameters (02-Contact.p2dat)
02-Contact.p2dat assigns stiffness, strength and friction parameters to the particle assembly and sets up the relevant contact models (parallel bonds, flatjoints, smoothjoints, etc.).
4.1) Material parameters: For different materials (e.g., metamorphic rock, sandstone), the script defines contact Young's modulus (emod), normal-to-shear stiffness ratio (kratio), tensile strength (ten), cohesion, friction angle (fa), bond gap and friction coefficient (fric).
5.2) Contact and bond models: Using cmat and contact method bond, the script assigns linear, parallel-bond, flatjoint and smoothjoint models to ball–ball and ball–wall contacts, and maps the above parameters to each material combination.
6.3) State saving: After assigning the contact parameters, the model is saved as 02-FJ-SJ-bond_Add, which is then used by 03-Rongshi.p2dat as the base bonded-rock sample.
4. Rock-State Saving and Cavity Creation (03-Rongshi.p2dat and 04-Dakong.p2dat)
7.1) 03-Rongshi.p2dat: Restores 02-FJ-SJ-bond_Add and saves it as 03-Rongshi. This file represents the reference bonded-rock sample before introducing flow-specific modifications.
8.2) 04-Dakong.p2dat: Restores 03-Rongshi and deletes particles within a given radius around the sample centre. This operation drills a cavity or “wellbore” that later serves as a flow boundary. The updated model is saved as 04-Dakong.
5. Darcy Flow Loading and Boundary Conditions (05-Daxi.p2dat)
05-Daxi.p2dat is the core Darcy-flow script. It applies flow boundaries, controls loading, and records histories.
9.1) Model restore and wall reset: The script restores the 04-Dakong state and deletes existing walls, preparing for the application of new boundary conditions.
10.2) Loading and strength-related parameters: Using loading_rate and related coefficients (xishu_e, xishu_s), the script sets upper bounds for stiffness and strength (emod_max, pb_emod_max, pb_ten_m, pb_ten_c) so that different experimental conditions can be matched.
11.3) Flow boundary and time-step settings: A function such as flow_bc_circle is called to apply a flow boundary at the central cavity. The gap_mul parameter adjusts the relation between fracture aperture and flow rate. The script defines the time step, mechanical time and several histories (e.g., time, flow-related variables, and crack_num), runs a specified number of cycles, and finally saves the model as darcy_test.
6. Domain Identification and Pressure Update (dom.p2dat and dom1.p2dat)
dom.p2dat and dom1.p2dat form a FISH toolkit for flow-domain identification and pressure/volume updates.
12.1) dom.p2dat: By scanning all contacts, the script builds connectivity information and groups particles that are connected into “domains”. The domain with the largest number of particles is treated as the outer domain, representing the external or infinite boundary region.
13.2) dom1.p2dat: For each domain, a pressure is defined and updated. For each contact between domains, the code calculates a pipe flow rate based on the pressure difference, an effective permeability factor and the contact aperture. The associated volume changes are accumulated for the two domains to enforce global volume balance. Pressure gradients are then converted into equivalent forces acting on the particles, enabling fully coupled fluid–solid interaction.
7. Fracture Statistics and Floater Removal (crack_num.p2dat and fracture.p2dat)
14.1) crack_num.p2dat: This script tracks bond-break events in BPM/CBM/FJM/SJM contacts during the simulation. It identifies the contact model (linearcbond, linearpbond, flatjoint, smoothjoint, etc.), distinguishes between tensile and shear failures, and accumulates separate counters for each type. The fragment and ball-result logic is used to record fragment IDs. Global variables such as crack_num are exposed so that they can be stored as histories in 05-Daxi.p2dat.
15.2) fracture.p2dat: Despite its name, this script mainly implements the floater-elimination algorithm. Functions such as flt_eliminate and flt_num identify particles with fewer than a specified number of contacts and adjust or remove them. Internal counters track the number of floating particles, active particles and contact forces. This procedure produces a denser, mechanically more realistic packing and reduces numerical noise in later flow and fracture simulations.
8. Usage Notes and Recommendations
16.1) Recommended execution sequence
Step 1: Run DOALL.p2dat to build the geometry, assign contacts, save the rock state and drill the central cavity (ending with the 04-Dakong state).
Step 2: Run 05-Daxi.p2dat to apply flow boundary conditions, perform Darcy flow and fracture evolution, and save the final darcy_test state along with history curves.
17.2) Micro-parameter tuning
To match different confining stresses or loading paths, adjust emod, cohesion and ten in 02-Contact.p2dat. To promote more tensile or shear fracture, reduce the corresponding strength parameters. Flow sensitivity can be controlled using the permeability factors in dom1.p2dat and parameters such as gap_mul in 05-Daxi.p2dat.
18.3) Post-processing
Histories typically include time, flow-related quantities (e.g., cumulative flow, pressure difference) and the number of fractures. It is recommended to export these data to text or spreadsheet files and analyze them with Python or MATLAB, for example to obtain permeability–time curves, fracture-count–strain curves, and the evolving proportion of tensile versus shear fractures.
19.4) Use as project and paper documentation
This English Word document can serve as both the user manual for the PFC2D Darcy-flow and fracture case and, after further polishing, as the basis for a “Numerical Model and Methods” section in a journal manuscript.
Appendix: Full .p2dat Script Listings
The following sections list the complete contents of all .p2dat files included in this project, for reference and archiving.
