# Uses:


 ## 1. RAW file Conversion:

This function takes a folder containing Thermo .RAW files and combines them into a single .csv file, averaging the scans within each file. This process eliminates the need to process each .RAW file individually. The arguments it takes are the folder path and the output .csv file path. The files that will be combined to a single .csv are recommended to be ordered alphabetically. 

Usage:
  
`average_raws_dump(input, output)`


`input` should be a folder containing .RAW files and `output` is the file path for the resulting .csv file. Writes a .csv file to the specified output path. Returns a list containing the spectra produced from the .RAW files. 

`average_raws(input)`


Identical to average_raws_dump except that it does not write any data to a .csv file. Returns a list containing the spectra produced from the .RAW files. 


## 2. Peak Extraction
This function identifies all the relative peaks in each spectrum of a .csv file and returns the m/z ratio and intensity value at each peak in each spectra in the form of another .csv file. The arguments it takes are the input file path and the output file path. It returns all the points where the intensity shifts from increasing to decreasing.

Usage:

`extract_peaks(input, header_size)`


`input` is the spectra list containing at least one spectrum with peaks that need to be extracted. `header_size` refers to the row number of the input csv where the actual data begins. Returns a list consisting of the peaks in each spectrum inputted. The spectra list must be in the form of a list containing at least one spectrum, where each spectrum is a list of mz-intensity pairs.


## 3. Relative Intensity Conversion

This function converts all intensity values in a spectrum list into relative intensities. 

Usage:

`standardiser(spectra_list, internal_standard, mass_error, header_size)`


Divides the intensity values in each spectra by the intensity of the `internal_standard`, which must be inputted. `mass_error` is used to determine whether or not the internal standard peak exists. If there is an mz value such that mz-mzImzI<ME, where mzI is the inputted mass to charge ratio of the internal standard, all intensities will be divided by the intensity at this mz value as a means of standardization. `header_size` refers to the row number of the input data where the actual data begins. The spectra list must be in the form of a list containing at least one spectrum, where each spectrum is a list of mz-intensity pairs.


## 4. Peak Comparison
This function is useful for comparing the relative intensities of m/z values persisting over multiple spectra. Given a treatment set of spectra and a control 	set of spectra, this function will return a .csv list of m/z values that are found exclusively in treatment along with the relative intensities of the m/z values in each spectrum. To speed up calculations and reduce noisy data, scans taken at the same time point are consolidated according to the experimental setup. The arguments it takes are the treatment file path, the control file path, filtering threshold, first pass average size, and second pass average size and identity tolerance. 

Usage:

`comparator(ctrl, trtmnt, mass_error, fpa, spa, thr, output_file = None)`

The comparator function takes a list of control spectra `ctrl`, a list of treatment spectra `trtmnt`, the `mass_error`, the first pass average `fpa`, the second pass average `spa`, threshold `thr`, and optional `output_file`. If `output_path` is unspecified, the results will not be written to a file. `thr` argument controls the factor by which the relative intensity must change to be considered significant. The `fpa` and `spa` refer to the experimental setup. If two readings were collected per sample, then `fpa` would equal 2. If three groups of samples were used, `spa` would equal 3. Refer to 3. Relative Intensity for the mass error definition. The control and treatment spectra lists must be standardised prior to this step. Method returns a list of m/z values found exclusively in treatment spectra. 

## 5. Naming and Formula Suggestions
This function will suggest molecular formulas and verify that they exist using PubChem, Chemspider, or SciFinder. Given a list of m/z values, it will return a .csv file containing the m/z values, their formulas, mass errors, unsaturation degrees where applicable, and IUPAC names. The arguments it takes are the structured dictionary of elements and expected ranges, the file path for a .csv m/z list, and the mass error tolerance. 
Usage:
naming(chosen_elements, mz_list, tolerance, output_file_path, search = None)
From a list of mz values, naming will convert them to monoisotopic masses before suggesting possible names and formulas. chosen_elements must be a dictionary consisting of elements and their respective compositional ranges. mz_list represents the list of mz values used for naming. outpful_file_path is the path of the .csv file containing the processed data. The search type is optional, or it can be “pcp”, “cs”, or  “CAS”. The function has no return value as all information is written to the output file. 

# Key Definitions
These definitions are important in fully understanding the usage scenarios.

## Max Mass Error (`MME`)
A measure of error defined for two mz values:

![MME](https://latex.codecogs.com/png.image?\dpi{110}&space;\frac{|mz_1&space;-&space;mz_2|}&space;{mz_1})

Mass errors must be less than `MME` for two mz values to be considered identical.
## First, Second Pass Averages (`fpa` & `spa`)

When comparing many spectra across multiple timepoints, it is useful to consolidate spectra based on type and timepoint in order to reduce noisy and greatly increase program efficiency. The diagram below represents an example of consolidation at a single timepoint where 6 spectra (blue) were used. When the first pass average is set to 2, the blue spectra are combined in pairs, resulting in the green spectra. Here, the seconnd pass average is set to 3, so the green spectra are combined into a single spectrum (orange).

![Diagram](/averaging_diagram.png)

## Threshold (`thr`)

When comparing the relative intensities across multiple timepoints and machine measurements, values within certain margins of each other are considered to be equal. For two intensity values, if they differ by a factor less than the threshold, they are reported as being approximately equal in the peak comparison step. 


## Spectra List

A spectra list is the basic format for spectral data for most of the functions. The standard Python list consists of at least one spectrum. Each spectrum is a list consisting of sublists of length 2. Except for those in the header, each of these two-member sublists contains the mz value at index 0 and the intensity at index 1. In the header, which is the first few sublists in a spectrum, any data may be present. Here is an example of a spectra list of 2 spectra with a header of size 3:

`spectrum_one = [['Spectrum 1',''], ['Example',''], ['mz', 'intensity'], [56.3423, .2342], [58.3453, .5346], [66.2342, 2347]]`

`spectrum_two = [['Spectrum 2',''], ['Example',''], ['mz', 'intensity'], [58.3892, .5788], [63.7894, .9974], [69.8741, 6837], [85.7643, .8888]]`

`spectra_list = [spectrum_one, spectrum_two]`


## Dependencies

NumPy, ortools, ChemSpiPy, CIRpy, and PubChemPy are all needed for full functionality. They can be installed using the following methods:

`pip install numpy`

`pip install ortools`

`pip install chemspipy`

`pip install cirpy`

`pip install pubchempy`



## An Example Script

For an example of how to use all the main functions, see example.py
