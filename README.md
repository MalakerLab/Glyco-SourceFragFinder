# Glycopeptide Source Fragmentation Finder

## Description
This repository includes a script used to denote potential instances of source fragmentation in glycoproteomic experiments. Its use is primarily suggested for the analysis of O-glycoproteomics, given that N-glycopeptides commonly coelute. This code is built to run in the **Python** framework. 

### Workflow
This script works by flagging glycopeptide identifications that elute within a given time frame of another identification with the same glycopeptide backbone structure but bearing a larger glycan mass. 

### Minimum System Requirements
The code functions in the Python environment. It uses the Pandas python library, so it must be downloaded prior to use, if not done so already. Further details on the installation can be found [here](https://pandas.pydata.org/docs/getting_started/install.html). 
### Minimum Data Requirements 
This script was developed to read search algorithm resulting files from an Excel document. Directory path for input file must be modified when the Excel spreadsheet is imported. The contents of different outputs might differ based on the search algorithm used. However, the minimum variables required for this script to function properly are the following:
1) **"PeptideBackboneSequence":** Contains glycopeptide backbone sequence, without any glycan modifications embedded in it. Other modifications, like oxidation, that might affect the retention time of a peptide should be included in this peptide sequence.
2) **"GlycanMass"**: Total glycan mass attached to the glycopeptide. All glycan masses should be reported in the same format, with the same number of decimal places. Otherwise it will be counted as a different glycan mass. 
3) **"Time":** Retention time of a given glycopeptide. Can be reported in minutes or seconds.
4) **"File Name":** Filename must be included in the input, especially when output from different files is grouped into one input Excel document. 

### Other data
Other data outputted by the search algorithm can be imported into the code, such that it is included in the output generated. All data that is wanted to be in the output should be included in the generation of the initial filtered data frame. Importantly, the time window selected to flag identifications can be modified in the script, by changing the variable *time_window* to the desired one. Must be in the same scale as the time in the input file.

### Output
Output generated can be modified as needed. In the current state of the code, a new column will be created in the Excel output file called *InSource?*. If the value assigned is *Normal*, this indicates that it did not originate from source fragmentation. If the value outputted is *Fragmentation*, this indicates that such identification could potentially originate from in-source fragmentation. The name of the output can be adjusted by the user, and it will be in the same directory as the initial input file.  

### Citation
If this script is used, please cite https://www.biorxiv.org/content/10.1101/2023.05.28.542648v1

## Use 
The input directory path and the name of the Excel document can be adjusted in this step.
```python
import pandas as pd
df=pd.read_excel(r'User_Filepath_SearchResults.xlsx')
```
Columns that want to be kept in the output file, and are required for the function of this script, are defined here. Users can change the names of these columns to match to those in their input file. Please refer to the previous section for minimum input requirements.  
```python
df=df[["File Name", 'Scan Number', 'Time', 'Precursor MZ', 'Precursor Charge', 'Full Sequence',
       'PeptideSequence', 'GlycanMass', 'Glycan','Modification Type(s)']]
```
The time window is user defined. Here, it is defined as one minute. 
```python
time_window = 1.0
```
```python
df['InSource?'] = 'Normal'
for _, group in groups:
    for i, row1 in group.iterrows():
        for j, row2 in group[(group['GlycanMass'] > row1['GlycanMass']) & (group['Glycan'] != row1['Glycan'])].iterrows():
            if abs(row1['Time'] - row2['Time']) <= time_window:
                    df.at[i, 'InSource?'] = 'Fragmentation'
 ```
Output is exported as an Excel file in the same directory of the input file. 
```python
df.to_excel('Output_File.xlsx', index=False)
