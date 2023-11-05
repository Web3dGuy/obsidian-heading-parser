## Obsidian Tools - Markdown heading parser
---
#python_helpers #format_tools #obsidian

- [Step Through/Survey Mode](#step-throughsurvey-mode)
- [Python Version](#python-version)
- [Options](#options)
- [Survey Commands](#survey-commands)
- [Restrictions](#restrictions)
- [Function Descriptions](#function-descriptions)
- [Examples](#examples)
- [License](#license)

## Turn This: 
![Heading questions, with answers](https://github.com/blazedforever/obsidian-heading-parser/blob/main/heading_parser_1.png?raw=true)
## Into This:
![Headings parsed into a table format](https://github.com/blazedforever/obsidian-heading-parser/blob/main/heading_parser_2.png?raw=true)

## Step Through/Survey Mode:

![Stepping through each heading and giving an answer in survey mode](https://github.com/blazedforever/obsidian-heading-parser/blob/main/heading_parser_3.png?raw=true)

### Python Version:

- The script was tested on `Python v3.11.5` but should work with `>= v3.0`.
### Options

- `--novalues` Leaves all value fields empty in the output.
- `--table` Formats the output in markdown table format.
- `--survey` Interactive mode that prompts for values for each attribute.
- `--help` Prints the manual/help for the script.
-  `--force` Disables error catching. File should start with a heading.

### Survey Commands

When in survey mode, the following commands can be used:

- `/skip` Skip the current attribute, leaving the value blank.
- `/asis` Leave the current attribute's value as is.
- `/end` End the survey, leaving the remaining attributes as they were.
- `/skipend` End the survey, skipping all remaining attributes and leaving them blank.

## Restrictions

Note that `--survey` and `--novalues` options cannot be used together as they are mutually exclusive.

## Function Descriptions

- `parse_markdown(md_content)`: Parses the Markdown content and returns structured data.
- `survey_mode(parsed_data)`: Interactive survey mode for user input.
- `create_output(parsed_data, output_file, novalues, table_format)`: Generates the output Markdown file based on the structured data.
- `print_help()`: Displays the help text for the script.

## Examples

Run the script to parse `example.md` and write to `output.md`:

`heading_parser.py example.md output.md`

Generate a table format output with empty values:

`heading_parser.py example.md output.md --table --novalues`

Run in survey mode:

`heading_parser.py example.md output.md --survey`

Generate a table format output:

`heading_parser.py example.md output.md --table`


## License

This script is provided "as is", without warranty of any kind. You may use, modify, and distribute it freely.

