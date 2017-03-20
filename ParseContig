# Command line program with input usage: "python ParseContig.py gbk-file output-file"
# Parses through a Contig GenBank file (.gbk) and returns a tab delimited file with all the gene information

import sys
import re
import os.path


class ParseContig(object):

    # RegEx patterns of the items of interest
    contig_pattern = re.compile("LOCUS\s*(\S+)")
    organism_pattern = re.compile("SOURCE\s+(\w+\s+\w+)")
    chromosome_pattern = re.compile("\/chromosome=(.+)")
    start_end_pattern = re.compile("gene\s+(complement)?\D*(\d+)\W+(\d+)")
    gene_pattern = re.compile("\/gene.+\"(\S+)\"")

    def __init__(self, gbk_file, out_file):
        try:
            self.gbk = open(gbk_file)
            self.ofh = open(out_file, 'w')
        except IOError:
            if not os.path.isfile(gbk_file):
                print("Rerun ParseContig request with a valid gbk file!")
                sys.exit()
            else:
                print("Incorrect file input!")

        self.parse_file()

    def parse_file(self):

        heading = "Contig\tOrganism\tChromosome\tGene\tStart\tEnd\tDirection"
        print >> self.ofh, heading

        data = contig = organism = chromosome = start = end = gene = direction = None
        previous_line = ""  # temporarily stores the previous line to be used if it is needed

        for line in self.gbk:
            line = line.strip()

            if line.startswith("\\"):  # marks the end of a contig
                data = contig = organism = chromosome = start = end = gene = direction = None

            if line.startswith("LOCUS"):  # marks the beginning of a new contig
                match = ParseContig.contig_pattern.search(line)

                if match:
                    contig = match.group(1)

            if line.startswith("SOURCE"):  # search for the source organism
                match = ParseContig.organism_pattern.search(line)

                if match:
                    organism = match.group(1)

            if line.startswith("/chromosome"):  # line contains the chromosome
                match = ParseContig.chromosome_pattern.search(line)

                if match:
                    chromosome = match.group(1)

            if line.startswith("/gene") and "gene" in previous_line:  # see if it is the line directly after a gene section of contig
                start = end = gene = direction = None
                line_match = ParseContig.gene_pattern.search(line)
                previous_match = ParseContig.start_end_pattern.search(previous_line)

                if line_match:
                    gene = line_match.group(1)

                if previous_match:
                    start = previous_match.group(2)
                    end = previous_match.group(3)

                    if "complement" in previous_match.group():  # looks for the direction of the gene
                        direction = "complement"

                    else:
                        direction = "coding"

                print>> self.ofh, "%s\t%s\t%s\t%s\t%s\t%s\t%s" % (contig, organism, chromosome, start, end, gene, direction)

            previous_line = line


if len(sys.argv) < 3:

    error_msg = "USAGE: python %s gbk-file output-file" % (sys.argv[0])
    print error_msg
    sys.exit()

input_file = sys.argv[1]
output_file = sys.argv[2]

A = ParseContig(input_file, output_file)

