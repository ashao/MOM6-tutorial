# Controlling MOM6 diagnostic output
## Introduction
The diagnostic manager controls which output fields are requested in the model,
their frequency (e.g. daily), which files to store them in, what type of time
operation to perform (e.g. time mean), and whether the diagnostics are in
native space or remapped to a different coordinate. This file describes and
demonstrates various aspects of controlling MOM6 output.

## `diag_table`: The diagnostic manager control file
This file controls the behavior of the diagnostic manager. An excerpt of one
used in `OM4_025` is shown below
```
OM4_SIS2_cgrid_025
1900 1 1 0 0 0

"ocean_annual",              12, "months",   12, "months", "time"
"ocean_annual_z",            12, "months",   12, "months", "time"
"ocean_model",  "uo",           "uo",               "ocean_annual",       "all", "mean", "none",2
"ocean_model_z","uo",           "uo",               "ocean_annual_z",     "all", "mean", "none",2
```
The first two lines describe the experiment note that these can be changed to
anything without affecting the output. The first line is the name of the
experiment and the second is the start date of the experiment. After these two
lines, the diag manager expects all remaining lines to follow the format of
*file entries* and *field entries*. Note that file entries and field entries
can be interspersed, but in general at GFDL all the file entries are near the
start of the `diag_table` and field entries follow. The following descriptions
of *file entries* and *field entries* are from the FMS documentation of the
diagnostic manager.

### File entries
```
  FORMATS FOR FILE ENTRIES (not all input values are used)
  ------------------------

"file_name", output_freq, "output_units", format, "time_units", "long_name",


output_freq:  > 0  output frequency in "output_units"
              = 0  output frequency every time step
              =-1  output frequency at end of run

output_units = units used for output frequency
               (years, months, days, minutes, hours, seconds)

time_units   = units used to label the time axis
               (days, minutes, hours, seconds)
```
The diagnostic manager expects that any diagnostic stored in an output file
should be at the same time frequency but any combination of spatial variables
is supported (i.e. 2D, 3D, U-grid, V-grid, H-grid.)

### Field entries
```
  FORMAT FOR FIELD ENTRIES (not all input values are used)
  ------------------------

"module_name", "field_name", "output_name", "file_name" "time_sampling", time_avg, "other_opts", packing

time_avg = .true. or .false.

packing  = 1  double precision
         = 2  float
         = 4  packed 16-bit integers
         = 8  packed 1-byte (not tested?)
```
* module\_name: refers to the model component the diagnostic is associated with.
  For MOM6, `ocean_model` refers to output on the model's native grid.
  Alternatively, if diagnostic coordinates have been setup in MOM\_input, a
  suffix would be added. `ocean_model_z` describes WOA09-like z-coordinate output
* field\_name: The MOM6 name for the diagnostic the diagnostic, note that for
  variables requested for OMIP/CMIP6 the CMOR name can also be used.
* output\_name: What the variable referred to by field\_name should be labeled in the output
* file\_name: The name of the file to store the data, note this also controls
  the frequency at which the diagnostic will be output 
* time\_sampling: ???, usually 'all'
* time_avg: The type of time operation to be done on the output
   * `.false.`, `none`, or `point`: Instantaneous snapshot of the variable at the given frequency
   * `.true.`, `mean`, `avg`, `average`: Time average
   * `pow`: The exponent to raise the output by, actual value set in "other\_opts" 
   * `rms`: Root mean square of the output 
   * `max` or `maximum`: The max over the time interval
   * `min` or `minimum`: The max over the time interval
   * `sum` or `cumsum`: The sum of the variable over the time interval 
* other\_opts: Can be used to specify spatial subsets of the model output or
  fields associated with time_avg
* packing: The precision at which to store the output (1: equivalent to REAL\*8
  or 2: equivalent to  REAL\*4)

## Setting up coordinates for for remapped diagnostics
The same regridding and remapping core used in the Arbitrary
Lagrangian-Eulerian step of the model is also used to remap diagnostics from
the model's native coordinate onto any runtime defined diagnostic grid. A
z-like diagnostic grid based on the World Ocean atlas is available by default
and diagnostics can be output onto that grid by specifying ocean_model_z as the
first entry in a 'field' statement. The OM4_025 test case has further examples
of outputting variables onto continuous isopycnal coordinates.

## Useful exercises
1. Explain in words what the excerpt from the diag_table means line by line
2. Include a new diagnostic from the Baltic\_OM4\_05 experiments
   available\_diags file. Output should be at 3 frequencies: 6 hours and 1 day.
   For 6 hour, output a snapshot, at 1 day the average
3. The same as 2, but also make z level output
