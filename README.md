# ABOUT
Information related to Cadence EDA tools eg. Virtuoso, ADE, Spectre, etc. Please note, Cadence EDA tools are proprietary and the information contained in this repository is primarily for users and will not contain any confidential or propeitary informaton.
# TOC
- [VIRTUOSO SCHEMATIC](#virtuoso-schematic)
  - [Parameterized Cells](#parameterized-cells)
  - [Bussed Wire Name Examples](#bussed-wire-name-examples)
  - [Adding a Net Expression for Inherited Nets](#adding-a-net-expression-for-inherited-nets)
  - [Passing Inherited Connection](#passing-inherited-connection)
- [VIRTUOSO ADE](#virtuoso-ade)
  - [Include a netlist in a schematic and simulate in ADE](#include-a-netlist-in-a-schematic-and-simulate-in-ade)

# VIRTUOSO SCHEMATIC 

## Parameterized Cells

Instruction on how to create a cell and pass parameter to it thought the symbol.

 - In the schematic, add `pPar("parameter")` instead of the value. For eg. if you wanted to parameterized the __width__ and __length__ of an inverter, instead of adding adding the values, add the following:
   - __Width__:`pPar("wid")`
   - __Length__: `pPar("len")`
 -  Add the labels in the __symbol__: `Create->Label`:
   -  Label:`width=[@wid:%:]`
     -  Label Type:`NLPLabel`
   -  Label:`length=[@len:%:]`
     -  Label Type:`NLPLabel`
 - Finally, edit the CDF. From the `icfb/Virtuoso` window, `Tools->CDF->Edit`:
   - Select CDF Type: `Base`
   - Library Name: `<library name here>`
   - Cell name: `<cell name here>`
   - On the table below fill a row for each parameter:
     - param Name: eg. `wid`
     - paramType: `float`
     - storeDefault:`yes`
     - name: `wid/len`
     - prompt: `wid/len`
     - defValue:`0.35e-6` (your value of choice. NOTE: Don't use abbreviations u,n,p.)
   - Click `OK`.
   - Now, when you click on the symbol, you should see two fields, `wid/len`, with their default values which can be overwritten by the user.

## Bussed Wire Name Examples 

Expanded Names for Wire, Net, and Pin Names 

 | Sample Name | Expanded Form |
 | --- | --- |
|  `<*2>term`  |  `term,term`  |
|  `<*2>(a,b),c`  |  `a,b,a,b,c`   |
|  `<*2>(a,<*2>b)`  |  `a,b,b,a,b,b`  |
|  `b<0:2>`  |  `b<0,1,2>` or `b<0>,b<1>,b<2>` |
|  `b<0:2:1>`   |  `b<0,1,2>`  |
|  `b<3:0:2>`  |  `b<3,1>`  |
|  `b<0:2*2>`   |  `b<0,0,1,1,2,2,>`  |
|  `b<(0:2)*2>`  |  `b<0,1,2,0,1,2>`  |
|  `b<0,2*2>`  |  `b<0,2,2>`  |
|  `b<(0,2)*2>`  |  `b<0,2,0,2>`  |
|  `b<0,1:3:4*1,2:2>`  |  `b<0,1,2>`  |
|  `b<0:1,2:2>`  |  `b<0,1,2>`  |


## Adding a Net Expression for Inherited Nets

- From the schematic menu select `Add`
- `Property Name`: eg. `power`. A given name which is used by netSet property to pass a net name.
- `Default Name`: eg. `vdd!` . The default name is usually a global name so it can used when no net names are passed to it.

## Passing Inherited Connection

- Instantiate the symbol of the top level schematic containing all the cells with inherited connections.
- If the net expressions in the schematics were not created by you (eg. standard cells), you need to find the name of the `netSet` property of the global instance (eg. `vdd`). You can select the pin of the vdd symbol (red square) and check the property and it should show up something as `[@ground_gnd:%:gnd!]` where the name of the inherited terminal is `ground_gnd` and the default netSet expression is `gnd!`.
- Now to set that global net to a physical pin/net, select the symbol, select `Edit -> Properties` and click `Add`
  - Name: ground_gnd
  - Type: netSet
  - Value: dgnd
  
  
  ### Alternate Method: Using Skill
  
  - `load("CreateNetExprLibrary.il")`  **FIXME**: Add CreateNetExprLibrary.il to this repo

```
    cv=dbOpenCellViewByType("myLib" "async_dig" "schematic" "" "a")
         CreateNetExpr("VDD" "[@VDD:%:VDD!]" cv)
         CreateNetExpr("VSS" "[@VSS:%:VSS!]" cv)
         schCheck(cv)
         dbSave(cv)
         dbClose(cv)
```



# VIRTUOSO ADE

## Include a netlist in a schematic and simulate in ADE

- Following instructions from [support.cadence.com](https://support.cadence.com/apex/ArticleAttachmentPortal?id=a1Od0000000nXEGEA2&pageName=ArticleContent)
- Open a new schematic window and place the pins (say, `vip`, `vin`) from your circuit in the schematic
- Create a symbol view for the text subcircuit. You do this from the schematic window by selecting `Create -> Cellview -> From Cellview`
- The Symbol Generation Options form will appear. Edit the fields in the Pin Specifications section, if desired (the default is all pins on the Top).
  - If creating this for an existing schematic/symbol (For eg. simulating a extracted netlist), copy the symbol pins, etc for the existing symbol.
- Under **Load/Save** Symbol Template Configuration, load the `artist` template.
- Edit the symbol as desired and save the symbol view.
- Make a copy of this symbol view and call the new view `spectre` or `eldoD`.
- Edit the CDF:
  - Set the CDF type to `Base` to open the base CDF for the cell. You will need "write" access to edit the CDF.
  - Under the Component Parameter section in the Edit CDF form, add a component parameter called `model`.
  - In the Add CDF Parameter form, specify only these values in order:
    - Name: model
    - Prompt: model
    - Type: string
    - Default: You can enter the subcircuit name
    - Parse as CEL: yes
    - Parse as number: no
    - Store Default: no
  - The "model" parameter holds the name of the subcircuit file to use during simulation for this cell. If you are finished adding parameters, click Apply.
  - If you are passing other parameters into the model file, add those as well. For example, in the image below, entries are added for gain and length as well. Click Apply
  - Edit the `Simulation Information` section of CDF for the `Spectre` or `eldoD` simulator (assuming you will simulate in `Spectre` or `eldoD`).
  - You must modify the simulation information to recognize the model property and support the parameters passed into the subcircuit file.
  - For example, choose spectre as the simulator and add the three instParameters: "model", "gain", and "length".
  - Note that spectre is chosen as the simulator. You can select another simulator, if desired, by clicking on the red arrow next to spectre.
  - The terminal names are already filled in for you in the termOrder field. If the terminal names are numerics like 1 2 3, you need to enclose them in double quotes like "1"  "2"  "3".
  - If you have instance parameters, add them in the instParameters field; for example, "gain" "length".
  - In the otherParameters field, type "model".
  - Click `OK` in the form.
- Instantiate the created symbol in a schematic and enter the name of the subcircuit as the model name.
  - (For example, the model name that you enter in the field might be `GPIO_TOP` if that's the subcircuit name in the netlist.
  - If you have added other parameters, such as gain and length, add them as well.
- Invoke the `Analog Design Environment (ADE)`.
- In the `Model Library Setup` form, provide the path to the file containing the text description of the subcircuit (that is, the netlist) through `Setup -> Model Libraries`.
  - Note: If the netlist is in Spice syntax and you are simulating using Spectre, at the top of the file, you should add the following statement:
```
simulator lang=spice
**
spice macro subckt netlist
```

