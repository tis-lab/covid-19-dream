ROBOT := java -jar bin/robot.jar

CURIFY = sed 's@<http://purl.obolibrary.org/obo/\([A-Za-z]*\)_\([0-9]*\)>@\1:\2@g'

all: bin/robot.jar mondo-closures.tsv mondo-cohd.tsv hp-snomed.tsv mondo-labels.tsv hp-labels.tsv

bin/:
	mkdir $@

tmp/:
	mkdir $@

bin/robot.jar: bin/
	curl -L -o bin/robot.jar \
		https://github.com/ontodev/robot/releases/latest/download/robot.jar


mondo-slim.owl: bin/robot.jar
	$(ROBOT) \
	  merge \
	    --input-iri http://purl.obolibrary.org/obo/mondo.owl \
	  remove \
	    --select "anonymous individuals <http://purl.obolibrary.org/obo/IAO*> <http://www.geneontology.org/formats/oboInOwl*>" \
	    --output $@

tmp/mondo-closures.tmp: mondo-slim.owl tmp/
	$(ROBOT) \
	  query \
	    --input $< \
	    --format tsv \
	    --query sparql/closures.rq $@

tmp/mondo-cohd.tmp: mondo-slim.owl tmp/
	$(ROBOT) \
          query \
	    --input $< \
	    --format tsv \
	    --query sparql/cohd.rq $@

tmp/mondo-labels.tmp: mondo-slim.owl tmp/
	$(ROBOT) \
          query \
	    --input $< \
	    --format tsv \
	    --query sparql/mondo-labels.rq $@

tmp/hp-labels.tmp: tmp/
	$(ROBOT) \
	  query \
            --input-iri http://purl.obolibrary.org/obo/hp.owl \
            --format tsv \
            --query sparql/hp-labels.rq $@

tmp/hp-snomed.tmp: tmp/
	$(ROBOT) \
	  query \
	    --input-iri http://purl.obolibrary.org/obo/hp.owl \
	    --format tsv \
	    --query sparql/snomed.rq $@

mondo-closures.tsv: tmp/mondo-closures.tmp
	$(CURIFY) $< | sed 's/?//g' | grep -v BFO > $@

mondo-cohd.tsv: tmp/mondo-cohd.tmp
	$(CURIFY) $< | sed 's/?//g' | sed 's/COHD://' > $@

hp-snomed.tsv: tmp/hp-snomed.tmp
	$(CURIFY) $< | sed 's/?//g' | sed 's/SNOMEDCT_US://' > $@

mondo-labels.tsv: tmp/mondo-labels.tmp
	$(CURIFY) $< | sed 's/?//g' > $@

hp-labels.tsv: tmp/hp-labels.tmp
	$(CURIFY) $< | sed 's/?//g' > $@

