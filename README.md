FALDO

A simple ontology to describe sequence feature positions and regiongs as found in 
GFF3, DBBJ,EMBL,GenBank files, UniProt, and many other bioinformatics resources.

The aim of this mini ontology is to only describe the position of a region or a feature.
It does not aim to describe features or regions itself. 
Instead it depends on resources such as the Sequence Ontology or the UniProt core ontolgy.

Examples


h3. An region where we know exactly where it starts and ends on the reference sequence.

```turtle
<_:1> a :Region ;
           :begin <_:1b> ;
           :end <_:1e> .

<_:1b> a :Position ; 
           a :ExactlyKnownPosition ;
           a :ForwardStrandPosition ;
            :position "1"^^xsd:int ;
            :reference ddbj:XXXDSDS .

<_1e> a :Position ; 
           a :ApproximatlyKnownPosition ;
           a :ForwardStrandPosition ;
           :begin <_:1ea> ;
           :end <_:1eb> ;
           :reference ddbj:XXXDSDS .

<_:1ea> a :Position ;
        a :ExactlyKnownPosition ;
        a :ForwardStrandPosition ;
           :position "3"^^xsd:int ;
           :reference ddbj:XXXDSDS .

<_:1eb> a :Position ;
        a :ExactlyKnownPosition ;
        a :ForwardStrandPosition ;
           :position "7"^^xsd:int ;
           :reference ddbj:XXXDSDS .
```

h3. A Region where the begin is on one contig and the end on an other.

```turtle
<_:2> a :Region
           :begin <_:2b> ;
           :end <_:2e> .
<_:2b> a :Position ; 
            a :ExactlyKnownPosition ;
            :position "1"^^xsd:int ;
            :reference <_:contig17> .
<_2e> a :Position; 
           a :ExactlyKnownPosition ;
           :position "4"^^xsd:int ;
           :reference <_:contig29> .
```

h3. Propable position

Here we have a begin position that could be on one of two nucleotides, and this case we have
a probablisitic model that says that the feature could start at position 1 or 2. Its just that position2 
has a likelihood of 0.9 and position 1 a likelihood of 0.1. 

```turtle
<_:3> a :Region ;
           :begin <_:3b> ;
           :end <_:3e> .
<_:3b> a faldop:ProbablePosition ;
           faldop:posibilities(<_:3bp1>,<_:3bp2>) .
<_:3bp1> a faldop:ProbablePosition ;
           faldop:probability "0.1"^^xd:double ;
           faldop:location <_:3bb1> .
<_:3bp2> a faldop:ProbablePosition ;
           faldop:probability "0.9"^^xd:double ;
           faldop:location <_:3bb2> .
<_:3bb1> a :Position ;
            a :ExactlyKnown ;
            :position "1"^^xsd:int ;
            :reference <_:1Strand> .
<_:3bb2> a :Position ;
            a :ExactlyKnown ;
            :position "2"^^xsd:int ;
            :reference <_:1Strand> .
```
