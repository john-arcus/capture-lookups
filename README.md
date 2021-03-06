# capture-lookups
A Symfony Console command. Searches for configuration file that lists URLs of Google Sheets, grabs the Sheets and stores their data locally as CSV files.

Designed be used in the context of the Symfony Console application at https://github.com/forikal-uk/xml-authoring-tools which, in turn, is used in the context of a known directory structure which is based on [xml-authoring-project](https://github.com/forikal-uk/xml-authoring-project).


# Usage instructions


## Specifying the Lookup tables to collect

We assume this command is run in the context of an [xml-authoring-project](https://github.com/forikal-uk/xml-authoring-project). ie. the key aspects of the structure of the directory is known.

Use the `scapesettings.yaml` configuration file in the root of the xml-authoring-project to specify the mappings of the lookup files that we must collect.

(See sample at scapesettings.yaml.sample)

The key should define the name of the lookup table and the value should define the URL of the Google Sheet that represents the lookup table on GSuite.

* LookupTableA -> https://URL-to-Google-SheetA
* LookupTableB -> https://URL-to-Google-SheetB

## An example Google Sheet

For example, the console command will go and find [this kind of Google Sheet](https://docs.google.com/spreadsheets/d/1kU_R8RokoMy9qvJqxy72H58cS48EVs0zRJXcgTZ5YFI/edit?usp=sharing) and write the values as a CSV.

## Connecting to GSuite

The file that Google Api uses to authenticate access to GSuite should be in the root of the [xml-authoring-project](https://github.com/forikal-uk/xml-authoring-project).

(If they are not there, you can request they be created).


## Run the command

When the command is run, it will 

* Search for the scapesettings.yaml in the current working directory, if not found it will look in the parent recursively until a file named scapesettings.yaml is found.
* Determine the `DestinationDirectory` to write-to:
  * If `DestinationDirectory` option is passed to command, use that.
  * If no `DestinationDirectory` option is passed to command, set it to the default `DestinationDirectory` (see below). 
    * The default `DestinationDirectory` is the working directory in which the command was invoked. 
* For each Lookup table specified in the configuration file:
  * Go to the Google Sheet on GSuite
  * Determine the name of the Google Sheet
  * Check the name to ensure it is made of only alphanumeric characters, dot, hyphen or underscore. (i.e the name is less likely to cause issues if used as a filename on Windows or MacOS)
    * If the name contains invalid characters, write a meaningful error message to STD_OUT and STD_ERR and exit with an error code.
  * Check to see if a CSV file matching that name is already stored in the destination directory
  * If it is already present and the `-f` (--force) flag  is NOT set, ask user "Permission to overwrite the file y/n?". With the suggested default prompt being no, `[n]`.
  * If it is already present and the -f (--force) flag  is set, overwrite the existing file without prompting the user.
  * Else, create a file matching the name of the GSheet 
  * Write the contents of the Google Sheet as a CSV file. (comma delimeter, double quotes used to encapsulate strings)  


