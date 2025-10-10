# Python converter

The converter.py script takes an OPI file, runs the inbuilt Phoebus converter to convert OPI to BOB and then attempts to fix some of the issues that have been picked up in the conversion.
In some cases, changes have to be made to the OPI file BEFORE the conversion to BOB but the script will handle creating a tmp OPI file and deleting it when finished.
At the end of the script, it will report exactly what bits it had to fix in the BOB file.


## Installation
- Create the python venv:
  
	`python -m venv venv`
- Activate
  
	`source venv/bin/activate`
- Install xmltodict dependency
  
	`pip install xmltodict`


## Usage

### Basic usage:
	python converter.py -f <path/to/opi.opi>

### Options:
	--tfile: template file containing mapping for EDM symbols (e.g. how many symbols, image location, etc)
	--pname: name of the plot file when opening in plt file in the databrowser
	--fixGroup: if Phoebus cannot convert the GroupContainer widget correctly then use this option to try to fix it in the OPI before coverting.

- `--tfile`:
	- In the case that a screen use the DLS custom EDM Symbol widget, the script will first replace these instances in the OPI file with a CSS MultistateMonitorWidget widget. Then when the Phoebus converter runs, this will be converted to a Phoebus Symbol widget. If you don't do this then the converter will not recognise the widget and put in a 'placeholder' space whil also striping all rules, actions, scripts etc from the widget, which we want to keep.
	- To convert to a Symbol widget we also need to define a set of images for each pv state based off the original combined image used in the EDM symbol widget. This requires some human intervention to determine the input image, number of images/states, starting index etc. These need to be defined in a template file and passed into the script with this `-tfile` option. See the templates directory for some full examples.
     ```
  <symbol>
		<name>$(dom)-VA-RGA-01</name> # Name of the widget in the screen
		<image>../../../rga/rga-symbol-34.png</image> # Name of image to include in the widget properties
		<location>/home/raz28119/ph_test_conversion/FE16I/FE/FEApp/opi/rga/rga-symbol-34.png</location> # Location on input image
		<nimages>11</nimages> # Numebr of images/states to include
		<width>34</width> # Width of the subimage
		<height>34</height> # Height of the subimage
		<startindex>1</startindex> # Starting index 
		<invalidimageindex>0</invalidimageindex> # Which image index to use if pv values is invalid
	</symbol>
    ```
- `--pname`:
	- Tip is to use the $\(device\) as the name of the plot file name
- `--fixGroup`:
	If you run the converter and see an error like this: '', then try running with the `--fixGroup` option.
- Debugging:
	Within the script you can trun on detailed debugging by setting `debug=True` on line 11.
- No edit file:
	A list of files that the converter should not run on is defined in the 'no_edit_file'. This can be specified within the Python script on line 9. This usually consitutes of files that have been run through the converter but then have required manual updates afterwards. These files should not be run through the converter again as it would overwrite the manual changes made. For satefy the script with check this file first and stop an conversion if the input file matches a file in this list.
