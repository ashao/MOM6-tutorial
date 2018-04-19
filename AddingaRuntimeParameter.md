# Adding a runtime parameter to MOM6
## Introduction
Most of the MOM6 related options are **NOT** configured using a traditional Fortran namelist. While these types of namelists provide an easy programming interface, it is difficult to make the model self-documenting. The MOM6 solution is to instead read from a text file containing the runtime parameters. This tutorial demonstrates the changes to the source code needed to specify a Prandtl number when adding the turbulent diffusivity diagnosed from the energetic
planetary boundary layer scheme to the eddy viscosity used for momentum (line 819 of
src/MOM6/src/parameterizations/vertical/MOM_diabatic_driver.F90)

## Interface for getting/logging runtime parameters:
The interface for both getting the runtime parameter and setting the description of the runtime parameter is via
`get_param` in  `MOM6-examples/src/MOM6/src/framework/MOM_file_parser.F90`
```
subroutine get_param_real(CS, modulename, varname, value, desc, units, &
               default, fail_if_missing, do_not_read, do_not_log, &
               static_value, debuggingParam)
  type(param_file_type),      intent(in)    :: CS
  character(len=*),           intent(in)    :: modulename
  character(len=*),           intent(in)    :: varname
  real,                       intent(inout) :: value
  character(len=*), optional, intent(in)    :: desc, units
  real,             optional, intent(in)    :: default, static_value
  logical,          optional, intent(in)    :: fail_if_missing
  logical,          optional, intent(in)    :: do_not_read, do_not_log
  logical,          optional, intent(in)    :: debuggingParam
end subroutine get_param_real
```
 
## Tutorial: Adding and using a runtime parameter
1. Most of the time the runtime parameter will need to be stored in a control structure. Add the following lines to the
   control structure for MOM_diabatic_driver after line 10
```
  real    :: ePBL_Prandtl            !< The Prandtl number used to add the diffusivity 
                                     !! diagnosed from ePBL to the eddy viscosity used in
                                     !! the momentum equations
```
2. Now we will want to use the parameter to do some meaningful change to the model
* Modify line 822 (originally 819)
```
           visc%Kv_turb(i,j,K) = visc%Kv_turb(i,j,K) + Kd_ePBL(i,j,K)*CS%ePBL_Prandtl
```
* Modify line 825 (originally 822)
```
           visc%Kv_turb(i,j,K) = max(visc%Kv_turb(i,j,K), Kd_ePBL(i,j,K)*CS%ePBL_Prandtl)
```
3. Runtime parameters are usually read during the initialization of the module. For this case, add the following lines
   after line 1906
```
  call get_param(param_file, mod, "EPBL_PRANDTL", CS%ePBL_Prandtl, &
                 "If true, the diffusivity from ePBL is added to all\n"//&
                 "other diffusivities. Otherwise, the larger of kappa-\n"//&
                 "shear and ePBL diffusivities are used.", default=1.)
```
4. Recompile the model and run the MOM6 executable for an experiment that uses ePBL (e.g. Baltic_OM4_05)
5. Check MOM_parameter_doc.all and verify that EPBL_PRANDTL is now documented
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzM5MzYwODgyLDExMjgxNDA4NjRdfQ==
-->