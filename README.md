## Obsidian Tools - Markdown heading parser
---
#python_helpers #format_tools #obsidian
## Turn This: 
![[heading_parser_1.png]]
## Into This:
![[heading_parser_2.png]]

## Step Through/Survey Mode:

![[heading_parser_3.png]]

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

This script is provided "as is", without warranty of any kind. You may use, modify, and distribute it in accordance with the license provided with the source code.

## Python Code

Below is the Python code:

```python
import re
import sys
import os

def parse_markdown(md_content, force=False):
    # Check if there are any headings in the content
    if not re.search(r'^#+ ', md_content, re.MULTILINE) and not force:
        raise ValueError("No headings found in the content.")
    # Split content into sections based on headings
    sections = re.split(r'\n## ', md_content)
    if sections[0].strip() == '':
        sections.pop(0)
    if not sections and not force:
        raise ValueError("The content does not contain sections under headings.")
    parsed_data = []
    for section in sections:
        section_title, *section_content = section.split('\n')
        section_content = '\n'.join(section_content).strip()
        attributes_values = re.findall(r'### (.*?)\n(.*?)(?=\n### |\n## |$)', section_content, re.DOTALL)
        if not attributes_values and not force:
            raise ValueError(f"Section '{section_title}' does not contain any attributes.")
        parsed_data.append((section_title, attributes_values))
    return parsed_data

def survey_mode(parsed_data):
    print("Survey mode: Provide values for each attribute. Commands are /skip, /asis, /end, /skipend")
    skip_end_issued = False
    for section_index, (section_title, av_pairs) in enumerate(parsed_data):
        if skip_end_issued:
            # If /skipend was issued, set all remaining values to blank
            parsed_data[section_index] = (section_title, [(attr, "") for attr, _ in av_pairs])
            continue
        print(f"Section: {section_title}")
        for i, (attribute, original_value) in enumerate(av_pairs):
            if skip_end_issued:
                # If /skipend was issued, leave the rest blank
                av_pairs[i] = (attribute, "")
                continue
            response = input(f"Value for '{attribute}' (current: '{original_value}'): ")
            if response == "/end":
                return parsed_data
            elif response == "/skipend":
                # Leave current and all remaining attributes blank
                av_pairs[i] = (attribute, "")
                skip_end_issued = True
            elif response == "/skip":
                av_pairs[i] = (attribute, "")
            elif response != "/asis":
                av_pairs[i] = (attribute, response)
    
    return parsed_data

def create_output(parsed_data, output_file, novalues=False, table_format=False):
    with open(output_file, 'w') as md_file:
        for section_title, av_pairs in parsed_data:
            md_file.write(f'## {section_title}\n\n')
            if table_format:
                md_file.write('| Attribute | Value |\n')
                md_file.write('| --- | --- |\n')
                for attribute, value in av_pairs:
                    # Clear value if novalues is True
                    value_to_write = "" if novalues else value.strip()
                    md_file.write(f'| {attribute} | {value_to_write} |\n')
            else:
                for attribute, value in av_pairs:
                    md_file.write(f'### {attribute}\n')
                    # Clear value if novalues is True
                    value_to_write = "" if novalues else value.strip()
                    md_file.write(f'{value_to_write}\n\n')

def print_help():
    help_text = """
    Usage: python script.py <input_file.md> <output_file.md> [options]
    Options:
    --novalues       Leaves all value fields empty in the output.
    --table          Formats the output in markdown table format.
    --survey         Interactive mode that prompts for values for each attribute.
    Survey Commands:
    /skip           Skip the current attribute, leaving the value blank.
    /asis           Leave the current attribute's value as is.
    /end            End the survey, leaving the remaining attributes as they were.
    /skipend        End the survey, skipping all remaining attributes and leaving them blank.
    --help           Prints the manual/help for the script.
    Note:
    --survey and --novalues cannot be used together.
    """
    print(help_text)
if __name__ == '__main__':
    if '--help' in sys.argv or len(sys.argv) < 3:
        print_help()
        sys.exit(0)
    input_file = sys.argv[1]
    output_file = sys.argv[2]
    # Check for correct input file extension
    if not input_file.lower().endswith('.md'):
        print("Error: Input file must be a markdown file with a .md extension.")
        sys.exit(1)
    novalues = '--novalues' in sys.argv
    table_format = '--table' in sys.argv
    survey_mode_flag = '--survey' in sys.argv
    force_flag = '--force' in sys.argv
    if survey_mode_flag and novalues:
        print("Error: --survey and --novalues cannot be used together.")
        sys.exit(1)
    # Read the markdown content from the input file
    try:
        with open(input_file, 'r') as md_file:
            md_content = md_file.read()
    except IOError as e:
        print(f"Error: Unable to read file '{input_file}'. Make sure the file exists and you have read permissions.")
        sys.exit(1)
    # Parse the markdown content
    try:
        parsed_data = parse_markdown(md_content, force=force_flag)
    except ValueError as e:
        if not force_flag:
            print(f"Error: {e}")
            sys.exit(1)
    except Exception as e:
        print(f"An unexpected error occurred: {e}")
        sys.exit(1)
    # Enter survey mode if the flag is set
    if survey_mode_flag:
        parsed_data = survey_mode(parsed_data)
    # Create the output markdown
    create_output(parsed_data, output_file, novalues=novalues, table_format=table_format)

```
