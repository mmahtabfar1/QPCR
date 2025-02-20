# QPCR

Three python scripts aim at calculating the **Delta_Ct, Delta_Delta_Ct, Fold Changes, Student's t-test, and P-value**
produced by Quantitative real time polymerase chain reaction (qRT-PCR).

### Compute QPCR results from QPCR output
Notes:

1. For **ABi 7900** users, data column names must be 'Sample Name','Detector Name','Ct','Ct Mean'. But 'Ct StdEV' is optional.
2. For **ABi ViiA 7 or Q7** users, you can use `qpcrRead.py` to extract data computing results directly.
3. For **Other machines**, you could use `qpcrCalculate.py` to calculate results with you own data. the Input file format have to be
exactly the same as **test_interest_data.xls**.

You should specify **internal control** name and **experimental control** name for your data sets.
The following file formats are supported: **xls, xlsx, csv, txt**.

## Dependency

Python2.7 or Python3+ (I haven't tried this with python2 yet)

python modules:
* numpy
* scipy: for t-test
* pandas
* matplotlib: for plotting (to do)

These can be installed with pip if on mac.
I am not sure about windows, you might have to use conda to install the modules.

### To get help for each respective module:
```bash
    python qpcrRead.py -h

    python qpcrCalculate.py -h

```


#### Parameters for qpcrCalculate.py

Parameters:
```
    usage: qpcrCalculate.py [-h] -d DATA [-s SHEET] [-i IC] -e EC [-o OUT]
                            [-m {bioRep,techRep,dropOut,stat}] [--header HEAD]
                            [--tail TAIL] [--version]
```

Calculate Delta Ct, DDelta Ct, Fold Changes, P-values for QPCR results.
```
    optional arguments:
      -h, --help            show this help message and exit
      -d DATA, --data DATA  the file(s) you want to analysis. For multi-file
                            input, separate each file by comma.
      -s SHEET, --sheetName SHEET
                            str, int. the sheet name of your excel file you want
                            to analysis.Strings are used for sheet names, Integers
                            are used in zero-indexed sheet positions.
      -i IC, --internalControl IC
                            the internal control gene name of your sample, e.g.
                            GAPDH
      -e EC, --experimentalControl EC
                            the control group name which your want to compare,
                            e.g. hESC
      -o OUT, --outFileNamePrefix OUT
                            the output file name
      -m {bioRep,techRep,dropOut,stat}, --mode {bioRep,techRep,dropOut,stat}
                            calculation mode. Choose from {'bioRep',
                            'techRep','dropOut'.'stat'}.
                            'bioRep': using all data to calculate mean DeltaCT.
                            'techRep': only use first entry of replicates.
                            'dropOut': if sd < 0.5, reject outlier and recalculate mean CT.
                            'stat': statistical testing for each group vs experimental control.
                            Default: 'dropOut'.
      --header HEAD         Row (0-indexed) to use for the column labels of the
                            parsed DataFrame
      --tail TAIL           the tail rows of your excel file you want to skip
                            (0-indexed)
      --version             show program's version number and exit

```
## Usage

### Extract Data from ABI machine  Data output

**e.g.**  

```bash
    python qpcrRead.py -d test/h9_vii7_export.xls -o test/test_interest_data.xls
```

### Calculate Delta_Ct, Delta_Delta_Ct, Fold_Changes

To run this program successfully, please use output file which is generated by `qpcrRead.py`

For other qRT-QPCR output formats, you can reshape your own data structure the same as **test_interest_data.xls**. `qpcrCalculate.py` will also work for your input.

The following file formats are supported: **xls, xlsx, csv, txt**.

**e.g.**

```bash
    python qpcrCalculate.py -d test/test_interest_data.xls \
                            -i GAPDH \
                            -e H9_NT_LSB_D16 \
                            -m dropOut
                            -o test/20150625_NPC_Knockdown
```

### Perform Student's t-test from n independent experiments.
Note: use `qpcrCalulate.py -m {'bioRep','techRep','dropOut'} ` output results as 'stat' input.
or your own file contained column: `Delta Ct`. See example file in `test` folder.


The following file formats are supported: **xls, xlsx, csv, txt**.

**e.g.**

For 3 independent experiments (biological repicates), try:

```bash
    python qpcrCalculate.py -d test/input1.xls,test/input2.xls,test/input3.xls \
                            -e H9_NT_LSB_D16 \
                            -m stat \
                            -o test/output
```
For -d, you could combine each experiment (input1-3) into one single file.
```bash
    python qpcrCalculate.py -d test/input_combined.xls \
                            -e H9_NT_LSB_D16 \
                            -m stat \
                            -o test/output
```
