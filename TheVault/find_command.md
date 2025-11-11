- `find` command usage and flags. 
	1. common structure of find command: `find [path] [options] [expression]`
		1. path: where to start searching
		2. options: refining your search
		3. expression: criteria like filename, size etc
	2. `find` searches from the path provided recursively.
	3. To find `sample.txt` file in the `home` directory, command will be
		```bash
		find ~ -name "sample.txt"
		```
	4. You can use wildcard also in the find command but only with `-name` and `iname` option. 
		```bash
		find ~ -name "*.txt" # finds all the txt files in home
		```
	5. To find case insensitive names, use `-iname` option
		```bash
		find ~ -iname "heLLo.txt"
		```
	6. To find and delete with a confirmation option
		```bash
		find ~ -name "*.txt" -exec rm -i {} \;
		```
		1. `-exec`: runs command on each file found by the `find` command
		2. `{}`: placeholder for current file that find is processing
		3. `\;`: marks end of the `-exec` command, it's just escaped semicolon
	7. Search for empty files and directories
		```bash
		find ~ -empty
		```
	8. For finding files use `-type f` and for directories use `-type d`
	9. For finding files modified in last 7 days
		```bash
		find ~ -type f -mtime -7
		```