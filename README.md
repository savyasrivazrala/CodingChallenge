# BankruptcyWatch Coding Challenge

import argparse
import json
import sys
import xml.etree.ElementTree as ET

def parse_xml(file_path):
    addresses = []
    tree = ET.parse(file_path)
    root = tree.getroot()
    for record in root.findall('.//record'):
        address = {}
        for field in record:
            tag = field.tag.lower()
            if tag == 'name':
                address['name'] = field.text
            elif tag == 'organization':
                address['organization'] = field.text
            elif tag == 'street':
                address['street'] = field.text
            elif tag == 'city':
                address['city'] = field.text
            elif tag == 'county':
                address['county'] = field.text
            elif tag == 'state':
                address['state'] = field.text
            elif tag == 'zip':
                address['zip'] = field.text
        addresses.append(address)
    return addresses

# Implement parse_tsv and parse_txt functions similarly

def parse_tsv(file_path):
    addresses = []
    with open(file_path, 'r') as file:
        for line in file:
            fields = line.strip().split('\t')
            address = {
                'organization': fields[0],
                'street': fields[1],
                'city': fields[2],
                'state': fields[3],
                'zip': fields[4]
            }
            addresses.append(address)
    return addresses

def parse_txt(file_path):
    addresses = []
    with open(file_path, 'r') as file:
        for line in file:
            fields = line.strip().split(',')
            address = {
                'name': fields[0],
                'street': fields[1],
                'city': fields[2],
                'county': fields[3],
                'state': fields[4],
                'zip': fields[5]
            }
            addresses.append(address)
    return addresses

# Implement the main function to orchestrate parsing and output

def main():
    parser = argparse.ArgumentParser(description='Process some files.')
    parser.add_argument('files', nargs='+', help='list of file paths')
    args = parser.parse_args()

    if not args.files:
        sys.stderr.write("Error: No files provided.\n")
        sys.exit(1)

    addresses = []
    for file_path in args.files:
        try:
            if file_path.endswith('.xml'):
                addresses.extend(parse_xml(file_path))
            elif file_path.endswith('.tsv'):
                addresses.extend(parse_tsv(file_path))
            elif file_path.endswith('.txt'):
                addresses.extend(parse_txt(file_path))
            else:
                sys.stderr.write(f"Error: Unsupported file format: {file_path}\n")
                sys.exit(1)
        except Exception as e:
            sys.stderr.write(f"Error processing file {file_path}: {str(e)}\n")
            sys.exit(1)

    addresses.sort(key=lambda x: x.get('zip', ''))
    print(json.dumps(addresses, indent=2))

if __name__ == "__main__":
    main()

