# ABOUT
Information related to Cadence EDA tools eg. Virtuoso, ADE, Spectre, etc. Please note, Cadence EDA tools are proprietary and the information contained in this repository is primarily for users and will not contain any conficential or propeitary informaton.

# VIRTUOSO SCHEMATIC 

## Parameterized Cells

Instruction on how to create a cell and pass parameter to it thought the symbol.

 - In the schematic, add `pPar("parameter")` instead of the value. For eg. if you wanted to parameterized the __width__ and __length__ of an inverter, instead of adding adding the values, add the following:
   - __Width__:`pPar("wid")`
   - __Length__: `pPar("len")`
 -  Add the labels in the __symbol__: `Add->Label`:
   -  Label:`width=[@wid:%:]`
     -  Label Type:`NLPLabel`
   -  Label:`length=[@len:%:]`
     -  Label Type:`NLPLabel`
 - Finally, edit the CDF. From the `icfb` window, `CDF->Edit`:
   - CDF Type: `Base`
   - Library Name: `<library name here>`
   - Cell name: `<cell name here>`
   - Click `Add-> CDF Parameter`:
     - paramType: `float`
     - storeDefault:`yes`
     - name: `wid/len`
     - prompt: `wid/len`
     - defValue:`0.35u` (your value of choice)
   - Click `OK`.
   - Now, when you click on the symbol, you should see two fields, `wid/len`, with their default values which can be overwritten by the user.
