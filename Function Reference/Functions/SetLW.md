# SetLW

## Description
Procedure SetLW sets the line weight of the referenced object.

```pascal
PROCEDURE SetLW(
				h  : HANDLE;
				lw : INTEGER);
```

```python
def vs.SetLW(h, lw):
    return None
```

## Parameters
|Name|Type|Description|
|---|---|---|
|h|HANDLE|Handle to object|
|lw|INTEGER|Line weight to be applied to object (in mils).|

## Remarks
If you pass in the handle to a group, the objects within the group do not pickup the lineweight attribute. Compare this to the SetPenFore() procedure in which the objects within the group do pickup the colour attribute.

## Examples
#### VectorScript ####
```pascal
SetLW(ObjHd,12);
```
Better example:
```pascal
PROCEDURE LineWeightChange; 
{ (c) Petri Sakkinen 2008 }
CONST { substitute these with the required values }
oldWeight = 2; 
newWeight = 20;

PROCEDURE ChangeIt (h : HANDLE);
BEGIN 
SETLW(h, newWeight);
END; 

BEGIN
FOREACHOBJECT(ChangeIt, LW = oldWeight);
END; 

RUN(LineWeightChange);
```
And another example, from Pat Stanford:
```pascal
Procedure LW_Change;

{Set the FromLW and ToLW constants to the line weights (in mm)}

{That you want changed from and to}

{Vectorscript only handles lineweights in mils, so some trial } 
{and error may be required to determine the correct settings  } 
{March 13, 2008} {(c) 2008, Coviana, Inc - Pat Stanford pat@coviana.com} 
{Licensed under the GNU Lesser General Public License}

Const
FromLW = 0.05;
ToLW = 0.07;

Var
VFromLW, VToLW:Integer;

Procedure ChangeLW(H1:Handle);
Begin
If GetLW(H1)=VFromLW then SetLW(H1,VToLW);
end;

Begin
VFromLW := FromLW / 25.4 * 1000;
VToLW := ToLW / 25.4 * 1000;
{You may want to change the criteria on the next line if you }
{Don't want PIOs and Viewports to change}
ForEachObject(ChangeLW,(INSYMBOL &amp; INOBJECT &amp; INVIEWPORT &amp; (ALL))); 
end;

Run(LW_Change);
```
#### Python ####
```python
import vs

# This script sets the line weight of all objects inside selected symbols
# to a specified value (in mm). Select the symbols you want to update
# before running the script, and it will loop through each one,
# entering the symbol definition and updating every object inside.

# Set the target line weight in mm - change this value as needed
TARGET_LW_MM = 0.05

def set_line_weight_in_symbol(symbol_def_handle):
    """
    Loops through every object inside a symbol definition
    and sets its line weight to the target value.
    """
    # Convert mm to mils (VectorWorks internal unit for line weight)
    # Formula: mm / 25.4 * 1000 — round() used to avoid floor truncation
    # e.g. 0.05mm -> 1.9685 mils -> rounds to 2
    target_lw = round(TARGET_LW_MM / 25.4 * 1000)

    # Get the first object inside the symbol definition
    obj_handle = vs.FInSymDef(symbol_def_handle)

    # Loop through all objects in the symbol definition
    while obj_handle:
        # Apply the target line weight to this object
        vs.SetLW(obj_handle, target_lw)
        # Move to the next object in the symbol
        obj_handle = vs.NextObj(obj_handle)

def main():
    """
    Loops through all selected objects on the active layer.
    For each selected symbol instance, it retrieves the symbol
    definition and updates the line weight of all objects inside it.
    """
    # Get the first selected object on the active layer
    selected_obj_handle = vs.FSActLayer()

    # Loop through all selected objects
    while selected_obj_handle:
        # Check if the selected object is a symbol instance (type 15)
        if vs.GetTypeN(selected_obj_handle) == 15:

            # Get the name of the symbol and find its definition
            symbol_name = vs.GetSymName(selected_obj_handle)
            symbol_def_handle = vs.GetObject(symbol_name)

            # Confirm the definition exists and is a symbol definition (type 16)
            if symbol_def_handle and vs.GetTypeN(symbol_def_handle) == 16:
                # Update line weights for all objects inside this symbol
                set_line_weight_in_symbol(symbol_def_handle)

        # Move to the next selected object
        selected_obj_handle = vs.NextSObj(selected_obj_handle)

    # Notify the user that the script has completed
    vs.AlrtDialog(f"Line weight of objects in selected symbols set to {TARGET_LW_MM}mm.")

if __name__ == "__main__":
    main()
```

## Version
Availability: from All Versions

## Category
* [Object Attributes](../Categories/Object%20Attributes.md)
