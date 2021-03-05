# [LAMMPS](https://lammps.sandia.gov)-solvation-free-energy-via-decoupling-route

A minimum working example for computing the solvation free energy of ethanol (GAFF 1.7 and AM1-BCC charges) in water (TIP3P) based on *[Performing solvation free energy calculations in LAMMPS using the decoupling approach](https://doi.org/10.1007/s10822-020-00303-3)*

* Folder structure
  * *LJ* (scripts for LJ component of the solvation free energy calculation)  
  * *coul* (scripts for coulombic component of the solvation free energy calculation) 
    * *rerun_coul* (scripts for re-running trajectories generated using the *coul* scripts to compute the derivative for thermodynamic integration (T.I.))

Note: equations referred below correspond to the above article.

### LJ scripts
The *system.in* script includes the following files:
* *system.in.variables*     (define the variables to be used in the simulation)
* *system.in.init*          (define the relevant LAMMPS settings for initializing the simulation)
* *system.in.dat*           (define the system to simulate, i.e., atom coordinates and molecular topology)
* *system.in.coeffs*        (define the pairwise interaction coefficients/settings)
* *system.in.charges*       (assign the charges defined in *system.in.variables* to the appropriate atoms and/or atom types)
* *system.in.thermo*.       (define the thermodynamic commands for LAMMPS)
* *system.in.minimize*      (code to perform a set of energy minimizations)
* *system.in.minimize_br*   (code to perform a set of energy minimizations with *box_relax*)
* *system.in.ensemble*      (initialize velocities and define the ensemble to simulate via relevant fixes)
* *system.in.run*           (define the outputs to *dump* and *print*; and finally run the simulation)

##### Key points for LJ calc:
1. set the value of _lmbq_ [λ<sub>q</sub>] (**eqn. 6**) to 1e-9 (see *sytem.in.variables*) since zero will make *lmbcoul* (**eqn. 8**) ill-defined
1. we use the *[fep](https://lammps.sandia.gov/doc/compute_fep.html)* command to compute the derivative dUdlmbLJ [dU/dλ<sub>LJ</sub>] (**eqn. 2**) numerically.


### coul scripts
The *system.in* script includes the following files:
* *system.in.variables*
* *system.in.init*
* *system.in.dat*
* *system.in.coeffs*
* *system.in.charges*
* *system.in.thermo*
* *system.in.minimize*
* *system.in.minimize_br*
* *system.in.ensemble*
* *system.in.run*

##### Key points for coul calc:
1. set the value of *lmbLJ* [λ<sub>LJ</sub>] to 1.0 as the solute is fully coupled via the LJ interactions to the solution at this stage of the calculation
1. to compute the derivative *dUdlmbq* [dU/dλ<sub>q</sub>] (**eqn. 2**) we need to compute the coulombic interactions between a fully coupled solute with the solution over configurations we generated in the lmbq ensemble
1. to compute the above coulombic interactions we rerun the configurations generated using this script using the *rerun_coul* scripts


### rerun_coul scripts
The *system.in_rerun* script includes the following files:
* *system.in.variables_rerun*
* *system.in.init*
* *system.in.dat*
* *system.in.coeffs_rerun*
* *system.in.charges*
* *system.in.thermo*
* ~~*system.in.minimize*~~
* ~~*system.in.minimize_br*~~
* *system.in.ensemble_rerun*
* *system.in.run_rerun*

##### Key points for rerun_coul calc:
* We set ALL LJ interactions to zero (see *system.in.coeffs*) to capture only coulombic interactions
* We set *lmbq* [λ<sub>q</sub>] to 1.0 to compute the coulombic interactions of a fully coupled solute with the solution
* We make use of the *[group/group](https://lammps.sandia.gov/doc/compute_group_group.html)* command to compute the coulombic interaction between *solute* and *water* group of atoms


##### Created by Vikram Khanna, Doherty and Peters Research Groups, ChemE, UCSB; 08-Feb-2021
##### Tested and validated on LAMMPS Version 31-Mar-2017
