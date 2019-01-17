# Extract GO and KOG-class annotations from eggNOG-mapper output

[eggNOG-mapper](http://eggnogdb.embl.de/#/app/emapper) is the method of choice to annotate transcriptomes of non-model organisms.

This tiny repository provides one-liners for extacting specific annotation data (gene names, Gene Ontology [GO], and euKaryotic Orthologous Groups [KOG] ) out of eggNOG-mapper output table.

The goal is to generate annotations compatible with rank-based functional summary methods, [KOGMWU](https://cran.r-project.org/web/packages/KOGMWU/index.html) and [GO_MWU](https://github.com/z0on/GO_MWU).

See [Dixon et al, Science 2015](https://matzlab.weebly.com/uploads/7/6/2/2/76229469/dixon15-science.pdf) for examples of applying these methods.

Files included: 
* **kog_classes.txt** : correspondence table needed to convert single-letter KOG class codes into text annotations compatible with KOGMWU.
* **Mcavernosa_euk.emapper.annotations** : eggNOG-mapper output for [Montastrea cavernosa genome](https://www.dropbox.com/s/0inwmljv6ti643o/Mcavernosa_genome.tgz?dl=0) 


Extracting Gene Ontology annotations for [GO_MWU](https://github.com/z0on/GO_MWU):

```bash
awk -F "\t" 'BEGIN {OFS="\t" }{print $1,$6 }' Mcavernosa_euk.emapper.annotations | grep GO | perl -pe 's/,/;/g' >Mcavernosa_gene2go.tab
```

Extracting KOG annotations for [KOGMWU](https://cran.r-project.org/web/packages/KOGMWU/index.html):

```bash
#  KOG classes (single-letter):
awk -F "\t" 'BEGIN {OFS="\t" }{print $1,$12 }' Mcavernosa_euk.emapper.annotations | grep -Ev "[,#S]" >mc_gene2kogClass1.tab
# converting single-letter KOG classes to text understood by KOGMWU package (must have kog_classes.txt file in the same dir):
awk 'BEGIN {FS=OFS="\t"} NR==FNR {a[$1] = $2;next} {print $1,a[$2]}' kog_classes.txt mc_gene2kogClass1.tab > Mcavernosa_gene2kogClass.tab
```
Extracting gene names:

```bash
awk -F "\t" 'BEGIN {OFS="\t" }{print $1,$13 }' Mcavernosa_euk.emapper.annotations | grep -Ev "\tNA" >Mcavernosa_gene2geneName.tab
```

