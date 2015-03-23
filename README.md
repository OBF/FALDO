FALDO
=====

FALDO is the Feature Annotation Location Description Ontology.
It is a simple ontology to describe sequence feature positions and regions as found in 
[GFF3](http://www.sequenceontology.org/gff3.shtml), [DBBJ](http://www.ddbj.nig.ac.jp),
[EMBL](http://www.embl.org), [GenBank](http://www.ncbi.nlm.nih.gov/genbank) files,
[UniProt](http://www.uniprot.org), and many other bioinformatics resources.

The aim of this ontology is to describe the position of a sequence region or a feature.
It does not aim to describe features or regions itself, but instead depends on resources
such as the Sequence Ontology or the UniProt core ontolgy.

You can read more about it our [pre print describing FALDO](dx.doi.org/10.1101/002121)

Examples
--------

The examples in turtle avoid declaring prefixes for space reasons.

### Known positions
 faldo:Region
A genomic region where we know exactly where it starts and ends on the reference genome sequence:

```turtle
_:1 a faldo:Region ;
           faldo:begin _:1b ;
           faldo:end _:1e .

_:1b a faldo:Position ; 
           a faldo:ExactPosition ;
           a faldo:ForwardStrandPosition ;
            faldo:position 1 ;
            faldo:reference ddbj:XXXDSDS .

_:1e a faldo:Position ; 
           a :FuzzyPosition ;
           a :ForwardStrandPosition ;
           faldo:begin _:1ea ;
           faldo:end _:1eb ;
           faldo:reference ddbj:XXXDSDS .

_:1ea a faldo:Position ;
        a faldo:ExactPosition ;
        a faldo:ForwardStrandPosition ;
           faldo:position 3 ;
           faldo:reference ddbj:XXXDSDS .

_:1eb a faldo:Position ;
        a faldo:ExactPosition ;
        a faldo:ForwardStrandPosition ;
           faldo:position 7 ;
           faldo:reference ddbj:XXXDSDS .
```

A genomic region where the begin is on one contig and the end on an other:

```turtle
@prefix faldo: <http://biohackathon.org/resource/faldo> .

_:2 a  faldo:Region ;
         faldo:begin _:2b ;
         faldo:end _:2e .
_:2b a faldo:Position ,
         faldo:ExactPosition ;
         faldo:position 1 ;
         faldo:reference _:contig17 .
_:2e a faldo:Position ,
         faldo:ExactPosition ;
         faldo:position 4 ;
         faldo:reference _:contig29 .
```

A rather crucial difference with most begin and end conventions here they are biological begin and end. 
Not smallest number is start and the larger number is end.

```
----->increasing count of position
123456789012345678901234567890
actgacgactagatcgatcgatcgactagt

tgactgctgatctagctagctagctgatca
     <----- direction of transcription 
     |    |--transcription on reverse strand begins here
     |--transcription on reverse strand ends here      
```

For example the *cheY* gene in
Escherichia coli str. K-12 substr. [MG1655](http://www.ncbi.nlm.nih.gov/nuccore/NC_000913.2)
is described in the INSDC feature table as `complement(1965072..1965461)`,
which is 390 base pairs using inclusive one-based counting. In FALDO

```turtle
@prefix faldo: <http://biohackathon.org/resource/faldo> .
@prefix refseq: <http://identifiers.org/refseq/> .
@prefix uniprot: <http://purl.uniprot.org/core/> .

_:geneCheY a                     <http://purl.obolibrary.org/obo/SO_0000704> ; # A gene as defined by the Sequence Ontology.
                                   rdfs:label "cheY" ;
                                   faldo:location _:example .

uniprot:P0AE67 uniprot:encodedBy _:geneCheY .

_:example a                      faldo:Region ;
                                   faldo:begin _:example_b ;
                                   faldo:end _:example_e .

_:example_b a                    faldo:Position ,
                                   faldo:ExactPosition ,
                                   faldo:ReverseStrandPosition ;
                                   faldo:position 1965461 ; # Note that the biological start position is numerically greater than the end position.
                                   faldo:reference refseq:NC_000913.2 .

_:example_e a                    faldo:Position ,
                                 faldo:ExactPosition ,
                                 faldo:ReverseStrandPosition ;
                                 faldo:position 1965072 ; # Note that the biological end position is numerically less than the start position.
                                 faldo:reference refseq:NC_000913.2 .
```

### Fuzzy positions

Assume we have a protein aminoacid sequence "ACK" and a massspectrometry experiment says the amino acid 
A or C is glycosylated. But we don't know which of the two it is. We do know it is not "K".


```turtle
@prefix faldo: <http://biohackathon.org/resource/faldo> .
@prefix glycan: <http://purl.jp/bio/12/glyco/glycan> .
@prefix uniprot: <http://purl.uniprot.org/core/> .

_:glysolyatedAminoAcid a glycan:glycosylated_AA ; # The glycan ontology is used here.
                           faldo:location _:fuzzyPosition .
_:fuzzyPosition a        faldo:FuzzyPosition ,
                           faldo:InRangePosition ;
                           faldo:begin _:exactBegin ;
                           faldo:end   _:exactEnd .
_:faldoBegin a           faldo:ExactPosition ;
                           faldo:position 1 ;
                           faldo:refence _:sequence .
_:faldoEnd a             faldo:ExactPosition ;
                           faldo:position 2 ;
                           faldo:refence _:sequence .
_:sequence a             uniprot:Sequence ;
                           rdf:value "ACK" .
```
In the above example uniprot and glyco refer to the glycoprotein and uniprot schema's.

### Probabilistic fuzzy positions

Here we have a begin position that could be one of two nucleotides. This case uses
a probablisitic model that denotes that the feature could start at both positions 1 or 2. Position 1
has a likelihood of 0.1 and position 2 has a likelihood of 0.9. 

```turtle
@prefix faldo: <http://biohackathon.org/resource/faldo> .

_:3 a    faldo:Region ;
           faldo:begin _:3b ;
           faldo:end _:3e .

_:3b a   faldo:ProbablePosition ;
           faldo:posibilities ( _:3bp1 _:3bp2 ) .

_:3bp1 a faldo:ProbablePosition ;
           faldo:probability 0.1e0 ;
           faldo:location _:3bb1 .

_:3bp2 a faldo:ProbablePosition ;
           faldo:probability 0.9e0 ;
           faldo:location _:3bb2 .
_:3bb1 a faldo:Position ,
           faldo:ExactPosition ;
           faldo:position 1 ;
           faldo:reference _:1Strand .

_:3bb2 a faldo:Position ,
           faldo:ExactPosition ;
           faldo:position 2 ;
           faldo:reference _:1Strand .
```

License
-------

[![Creative Commons License](http://creativecommons.org/images/deed/nolaw.png)](http://creativecommons.org/publicdomain/zero/1.0/) This work is licensed under a [Creative Commons Zero 1.0 Public Domain Dedication](http://creativecommons.org/publicdomain/zero/1.0/).


###Conversion into different RDF/XML
FALDO being maintained in faldo.ttl  can be converted into RDF/XML using for example the rapper tools.

```
rapper -i turtle -o rdfxml-abbrev faldo.ttl > faldo.rdf
```

It can also be converted into rdfxml that can be transformed with the owl2xhtml.xsl stylesheet into a documentation webpage.
For that the following code is helpfull.

```
xsltproc owl2xhtml.xsl faldo.rdf > faldo.html
```
