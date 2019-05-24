# Self-documenting of MOM6
MOM6 has a number of features that makes it significantly easier to understand what runtime modifications and parameters of the model have been configured and what could be configured. Additionally, the diagnostic manager is setup so that any diagnostics that could be made available are.

# Runtime options/parameters
MOM6 uses Fortran namelists very sparingly, opting instead to provide expanded functionality via `MOM_input` and `MOM_override`.

## Input files
### `MOM_input`
contain keyword-value pairs that configure the model. Modules call a subroutine `get_param` that will attempt to read a given parameter from these files. If the value is not found, a default value will be used.
### `MOM_override`
This behaves similarly to `MOM_input` in that additional keyword-value pairs can be specified. If a value already appears in `MOM_input`, that file can be 'overriden' by specifying `#override` before keyword-value pair.

## Output files
### Parameter documenting files
All the following files have a brief description of what the keyword actually controls as defined in the original `get_param` call.
- `MOM_parameter_doc.short`: Contains every non-default keyword-value pair that the model actually read (i.e. it should be the combination of `MOM_input` and `MOM_override`).  Can be renamed `MOM_input` to define a unique configuration.
- `MOM_parameter_doc.all`: Contains the value of every configurable parameter of the model whether the value was set explicitly or a default value was used. Note: parameters that are read only if another parameter is read are NOT included
- `MOM_parameter_doc.layout`: Contains only the parameters modifying the domain decomposition of the model
- `MOM_parameter_doc.debugging`: Contains only parameters controlling debugging modes of the model
### Model diagnostics `available_diags`
Contains the name of every available diagnostic, a brief description, assumed units, cell methods, and whether the diagnostic was requested or not
-


