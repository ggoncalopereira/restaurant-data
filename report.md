# How it is done
In order to both merge and harmonize the restaurant data fetched from the given .csv files, this notebook/script performs the following steps:
1. Data Cleaning, Merging and Harmonization:
	- Every column is standardized by converting text to uppercase, removing any special characters (non-alphanumeric) and whitespace characters;
    - These columns are also uniformized (removing the suffix number) in order to simplify the eventual data union;
    - A new string column is added on each file ("origin"), with its contents being "file1" or "file2", depending on its original file. This is to simplify the output column generated in the next step, in order to identify whether that row comes from a specific file or from another.
	- Data is then merged entirely;
	- Rows with empty or NaN values are also discarded after unifying all data (though ideally we could follow-up later with what is described in the "future considerations" section, as we are losing data with this step).


2. Output columns:
	- The requested output columns are generated with the entirety of the data merged, grouping the dataset by its fields;
	- With the grouped data, we can work on aggregating the rows to:
		- Count the # of occurrences (to fetch the duplicates);
            - To do so, we aggregate the grouped data, now normalized, and count its duplicates
		- Check for existence of the origin file's flag (and apply a flag to indicate its presence on such).
            - This is done by averiguating whether the field actually exists via ```when(col("output") == "file1", True)```, then counting the no. of its occurrences. When it does exist at least once, it will output `True`
	- With the dataset finalized, we can now generate a UUID/hash for the first four uniformized fields, which is done via a sha256 hash.


3. Final Output:
	- The merged data is then written to a CSV file.

# Issues, Future Considerations and "What I would do if given more time"
### Data given is very "repetitive" with its duplicates, some fields are very similar despite having different names
    An alternative to deal with this issue could be via a fuzzy matching algorithm to account for less "false positives" when it comes to name comparisons
### Some addresses are partly written, with the same address being written in different ways (some shortened, some in full, for instance)
	To tackle this issue, there could be some better string normalization libraries to normalize the addresses in its entirety
	We could also geocode addresses (with APIs like Google Maps or others as such) to minimize duplicates
### One column is generated per file in order to identify its origin, on the final dataset
    This could be problematic in the way that, if we have 10000 input files, we'd have 10000 additional columns (making it cumbersome to manage the final dataset)
        
    Alternatively, we could either have an array, appending each occurrence to itself
        __or__
    We could still keep a string field and just concatenate each file to itself (making it "File1.csv + File2.csv + ... + File10000.csv" if it was present on every file)