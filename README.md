# ABOUT
Information related to Cadence EDA tools eg. Virtuoso, ADE, Spectre, etc. Please note, Cadence EDA tools are proprietary and the information contained in this repository is primarily for users and will not contain any confidential or propeitary informaton.

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
|  Sample Name  | Expanded Form  |
|  <\*2>term  |  term,term  |
|  <\*2>(a,b),c  |  a,b,a,b,c   |
|  <\*2>(a,<\*2>b)  |  a,b,b,a,b,b  |
|  b<0:2>  |  b<0,1,2> or b<0>,b<1>,b<2> |
|  b<0:2:1>   |  b<0,1,2>  |
|  b<3:0:2>  |  b<3,1>  |
|  b<0:2\*2>   |  b<0,0,1,1,2,2,>  |
|  b<(0:2)\*2>  |  b<0,1,2,0,1,2>  |
|  b<0,2\*2>  |  b<0,2,2>  |
|  b<(0,2)\*2>  |  b<0,2,0,2>  |
|  b<0,1:3:4\*1,2:2>  |  b<0,1,2>  |
|  b<0:1,2:2>  |  b<0,1,2>  |
