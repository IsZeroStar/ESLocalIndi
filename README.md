# The Package for Measuring Bivariate Spatial Association at the Local Level
The ***ESLocalIndi*** open source package, developed for assessing spatial associations at local levels, is specifically designed to streamline the process of identifying spatial relationships (colocation, isolation, and random) between two types of geographic objects.

## Installation and Detials
Before install, ensuring you have Python 3.7 environment or later installed on your system. 
To install, please run the following command in your terminal:
```shell
pip install ESLocalIndi
```
For detailed descriptions of all function calls, you can use this command to see, where 'fuction' is a reference, not a really function.
```shell
#Detail Description
ESLocalIndi.function -h

#Function within ESLocalIndi
"ESLocalIndi.DLI" 
"ESLocalIndi.GWDLI" 
"ESLocalIndi.KLI" 
"ESLocalIndi.GWKLI"
"ESLocalIndi.sta_sig"
"ESLocalIndi.cal_sl"
```

## Preparation Work
### Data Description
Before using the package, you need to prepare the input data in CSV file format. The file must include the fields ["OBJECTID"],["m_lng"], ["m_lat"], and ["Type"], which represent the longitude, latitude, and type of the geographical objects, respectively. Below is an example of CSV data for crime events, which stored in the directory 'test'.
```markdown
OBJECTID,m_lng,m_lat,Type
0,-75.27518595,39.97726723,Other Assaults
1,-75.27390176,39.97614952,Other Assaults
2,-75.27357095,39.97827054,Other Assaults
3,-75.27328885,39.97655356,Vandalism/Criminal Mischief
.......
96,-75.25330673,39.98434846,Theft from Vehicle
97,-75.25330187,39.97244187,Other Assaults
98,-75.25321101,39.89972134,All Other Offenses
99,-75.25318981,39.97739067,Burglary Residential
```

### Create the spatial neighborhoods
Before calculating local indicators, you need to create a TXT file that specifies the distances between each geographic object and its neighbors. Each row in the TXT file corresponds to the spatial neighborhood of the geographic object in the row of the CSV file. The format for each line is: 'CSV line number 1,distance 1; CSV line number 2,distance 2'. The distance can be either the road network distance, as used in our paper, or the Euclidean distance, depending on your choice.  Below is an example of TXT file, which stored the distances between each sample crime event and its neighbors.
```markdown
0,0.00;1,146.81;2,182.50;3,212.72;8,328.38;9,383.13;10,412.49;
1,0.00;3,69.37;2,70.63;0,146.81;8,197.43;9,241.97;10,267.12;4,316.00;
2,0.00;3,58.03;1,70.63;8,145.96;0,182.50;9,204.76;10,240.88;5,283.92;
```

## Usage
### Calculate the local indicators and observed *p*-value
We provide four local indicators and two methods for calculating the left and right *p*-values of local indicators and observations. 
These four indicators correspond to four spatial proximity strategies: ***KLI***(*k* nearest neighbors), ***DLI***(spatial distance), ***GWKLI***(geographically weighted *k* nearest neighbors), and ***GWDLI***(geographically weighted spatial distance). 
You can use the methods either directly through the **Command-Line Interface** (***CLI***) or by importing them into the ***Python interpreter***. The function calls for the four local indicators are shown below.

```shell
# CLI Example
# k nearest neighbors and geographically weighted k nearest neighbors
ESLocalIndi.KLI/ESLocalIndi.GWKLI --txt_file TXT_FILE --csv_file CSV_FILE --k K --output_file OUTPUT_FILE
# spatial distance and geographically weighted spatial distance
ESLocalIndi.DLI/ESLocalIndi.GWDLI --txt_file TXT_FILE --csv_file CSV_FILE --d D --output_file OUTPUT_FILE
```
```python
# Python Interpreter Example
import ESLocalIndi
txt_file =".\\test\\sample_neighbors.txt"
csv_file = ".\\test\\sample_data.csv"
d = 400
output_file = ".\\GWDLI_400.csv"
# Calling function needs to use like the "gwdli_csv or kli_csv"
ESLocalIndi.gwdli_csv(txt_file,csv_file,d,output_file)
```
The output CSV file as shown in below. The output value format is ( local indicator,( left *p*-value, right *p*-value)).

```markdown
OBJECTID,Type,m_lng,m_lat,Other Assaults,Vandalism/Criminal Mischief,
10,Theft from Vehicle,-75.27151094,39.97574262,"(2.259619405386149, (0.9926555137902112, 0.04074155911335395))","(1.2792906868272702, (0.8531814025153381, 0.5371630300080192))"
21,Theft from Vehicle,-75.26702311,39.97215314,"(1.3959026301409576, (0.8479348645414289, 0.3897786368273055))","(3.9882418810701776, (0.9983304877183513, 0.021350744173582712))"
```
### Perform significance tests
Next, you can use a function to filter the significant local indicator values. Specifically, values are significant when the value is less than 1 and the left *p*-value is smaller than the significance level *α*, or when the value is greater than 1 and the right *p*-value is smaller than *α*.  The calling function is shown in below and the implementation principle is shown in the figure.
```shell
# CLI
ESLocalIndi.sta_sig --input_file INPUT_FILE --a A --output_file OUTPUT_FILE
```
```python
# Python Interpreter
from ESLocalIndi import *
sig_csv(input_file_path, a, output_file_path)
```
<img src="./docs/p-fig.jpg" alt="p-fig" style="zoom:30%;" />

### Calculate the strength of local spatial association
Finally, you can describe the strength of the local spatial association by calling this function using ***SL*** as we defined it. 
```shell
# i represents the local indicator starting from the i-th column in the CSV file.
ESLocalIndi.cal_sl --input_file INPUT_FILE --i I --output_file OUTPUT_FILE
```
```python
import ESLocalIndi
ESLocalIndi.sl_csv(input_file, i, output_file)
```

## Important Notes
The fields in the txt file and csv file must strictly follow the format defined above. Since both the local index value and the observed P-value are calculated at the same time, it may take some time, but it is faster and more exact than Monte Carlo simulations method.

## Contributing
Contributions are welcome! Feel free to open a pull request or submit an issue on GitHub. All contributions must be released under the MIT license.
