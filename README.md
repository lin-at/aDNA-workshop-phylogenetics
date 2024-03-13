# Introduction to phylogenetics with ancient mitochondrial genomes
In this exercise you will learn how to create a consensus sequence using ANGSD, which is a very handy software capable of handling NGS data. You will also learn how to test for the best substitution model for an alignment, and then learn how to create Maximum-Likelihood phylogenetic trees using your alignmnt of ancient and modern mtDNA sequences.

# Create a consensus sequence
First you will learn how to use ANGSD to call a consensus sequence. In practice, you will often have multiple libraries of the same sample so you will need to create a consensus sequence of either the entire genome (or the mtDNA only, if you have previously mapped to the mtDNA reference, assigned read groups, filtered, removed duplicates, indexed the bam etc.  

In your AWS home directory, create a new ```mtDNA``` directory:
```
mkdir mtDNA
cd mtDNA
```


```
# software
ANGSD="/home/ec2-user/Software/angsd/angsd"
BAMFILE="/home/ec2-user/Data/BAMS/Wolf_Iberian.bam"
```
We will call ANGSD to create a consensus sequence from your bam file.

```
$ANGSD -minQ 20 -minMapQ 30 -doCounts 1 -setMinDepth 3 -doFasta 2 -basesPerLine 60 -i $BAMFILE -out Wolf_Iberian.bam 
```

```-minQ 20``` mininum base quality

```-minMapQ 30``` minimum MapQ/mapping quality 

```-doCounts 1``` counts bases accross individuals to determine the concensus allele, this is required in order to build a consensus sequence

```-setMinDepth 3``` minimum depth of coverage for a site to be included

```-doFasta 2``` 2 indicates you want a majority-count consensus sequence, if you want a secuence with a random base you can use 1 instead

```-basesPerLine 60``` Bases per line that you want printed in the output

```-bam``` bam input

```-out``` basename of the output

Once ANGSD is done running, you’ll have a compressed FASTA file of your sample. 

Next, we will use IQTREE to determine what is the best substitution model to use for our (provided) alignment of canid mtDNA. Then, we will use RaXML and IQTREE to build Maximum-Likelihood trees. 



# Selecting the best-fit model
Download ```/home/ec2-user/Data/mtDNA/wolves_dogs_aln_noindels.phy```and upload this file into your newly created ```mtDNA``` directory in your AWS account. 
```
scp -i apgc-2021-key.pem.txt your_user_name@3.249.84.19:/home/ec2-user/Data/mtDNA/wolves_dogs_aln_noindels.phy .
```
```
scp -i apgc-2021-key.pem.txt wolves_dogs_aln_noindels.phy your_user_name@3.249.84.19:/home/your_user_name/mtDNA/
```
Note: use ```iqtree --help``` to display what the different options are.

```
# software
iqtree="/home/ec2-user/Software/iqtree/iqtree-1.6.12-Linux/bin/iqtree"
```

Run the below command to determine what the best-fit model is in IQTREE, using the standard model selection operator ```-m TESTONLY```. This model selection test is most similar to other model test programs like jModelTest and ProtTest. 
```
iqtree -s wolves_dogs_aln_noindels.phy -m TESTONLY
```
You can view the output of the test in the log file, ```wolves_dogs_aln_noindels.phy.log```. Let's see what the best-fit model is.
```
grep "Best-fit" wolves_dogs_aln_noindels.phy.log
```
According to the IQTREE test, the ```Best-fit model: HKY+F+I+G4 chosen according to BIC.``` BIC stands for _Bayesian Information Criterion_ (BIC; Schwarz, 1978) which is closely related to the _Akaike Information Criterion_ (also known as AIC). When scoring the fit of a model, the AIC measures the relative amount of information that is lost when that model is used to approximate reality. Consequently, the model with the minimum AIC score is preferred. To calculate the relative plausibility of different models, you use the difference in the AIC scores across the models and this leads to Akaike weights. You start with the model with the highest weight (lowest AIC score), and then determine the plausible set of models by successively adding the next highest weight until a cumulative weight of 0.95 is reached. BIC uses a different theoretical framework, but it is still similar to BIC in that BIC scores and weights are interpreted similarily. The major difference is that BIC generally penalizes complex models and ends up favoring simpler models. In other words, BIC can be preferable because when fitting models, you can end up overfitting the model by adding too many parameters in the aim of increase the likelihood. BIC solves this issue by introducing a greater penalty for more parameters you introduce.  

# Building a Maximum-Likelihood phylgenetic tree with IQTREE
There are many different programs you can use to build a ML tree. One very flexible ML-based method is <a href="http://www.iqtree.org/">IQTREE</a>. IQTREE is great, has extensive documentation, and also integrates model selection with a wide variety of different models, so you don't have to use a separate model tester. 

Now, let's make a tree using this model, with with 1000 bootstrap replicates (ultra-fast bootstrap option), which is what the IQTREE manual recommends.  
```
iqtree -s wolves_dogs_aln_noindels.phy -m HKY+F+I+G4 -bb 1000 -redo
```

Use ```-redo``` option if you really want to redo the analysis and overwrite all output files. (Yes). This will take a few minutes.

When IQTREE is finished running, download ```wolves_dogs_aln_noindels.phy.treefile``` onto your local computer with the command:
```
scp -i apgc-2021-key.pem.txt your_user_name@3.249.84.19:/home/your_user_name/mtDNA/wolves_dogs_aln_noindels.phy.treefile .
```
# Building a Maximum-Likelihood phylogenetic tree with RAxML
As I have previously mentioned, there are many different programs you can use to build a ML tree. <a href="https://cme.h-its.org/exelixis/web/software/raxml/index.html">RAxML</a> is one of the most popular ML-based method in phylogenetics. Unfortunately, the substitution models you can implement in RAxML are much more limited, and HKY+F+I+G4 model identified in IQTREE cannot be fully used. But as an example, let's go with ```-m GTRGAMMA --HKY85```. It will take almost 1 hour to run this on the AWS, so we're not going to do it during the workshop. However, we've generated the tree for you, using the command below:


```
raxmlHPC-SSE3 -f a -x 12345 -m GTRGAMMA -p 12345 -# 100 -s wolves_dogs_aln_noindels.phy -n raxml_tree --HKY85
```
Here are what the different operators are: 

```-f``` selects the algorithm to use. The ```a``` argument selects a rapid bootstrap analysis and best-tree search in one run.

```-x``` random seed for rapid bootstrapping. $RANDOM is a UNIX variable that specifies a random number. We choose 12345. 

```-p``` random seed for parsimony inferences. We choose 12345. 

```-#``` number of alternative runs on distinct starting trees. We choose 100 starting trees. 

```-m``` select the model of evolution. ```GTRGAMMA``` specifies the general-time-reversible nucleotide substitution model with a gamma model of rate heterogeneity. Compared to IQTREE, ```GTRGAMMA``` and similar variations of it (e.g. ```GTRCAT```) are really the only substitution models RaXML offers.

```-s``` input alignment file.

```-n``` name of output file.

You can download the best-scoring ML tree with support values: 
```
scp -i apgc-2021-key.pem.txt your_user_name@3.249.84.19:/home/testusr/mtDNA/RAxML_bipartitions.raxml_tree .
```



# Visualizing your tree
Open **FigTree**.
 Enter "bootstrap" (sorry for my typo!) in the pop-up window.
    ![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/7f633e10-b5eb-493a-ba11-a554e325c8ec)
At the top click on the button that says "Node" and click on the branch for the clade of the 4 coyote sequences so that it is higlighted in purple. These are the outgroup sequences that roots the topology of the tree.
![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/44e459e7-22f6-4a08-9e13-fd2480f2ac89)
Click on the button that says "Reroot  on the top left.
On the Layout panel on the left, click the little carrot triangle next to "Trees" and check the box  for "Order nodes". On the "Ordering" pull-down menu, select "increasing". This is just to order the tree branches in a more visually organized way.
![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/a322088d-634d-41e8-914a-40fdd51ce72f)
On the Layout panel on the left, click the little carrot triangle next to "Node Labels" and check the box. Select "bootstrap" on the "Display" pull-down menu. 
 ![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/5ee4bf4c-a7d9-4db7-9c0b-c2dc81d2ad70)
You can manipulate the tree by zooming in/expanding the branches using the "Layout" panel on the left by adjusting the "Zoom" and "Expansion" toggles. Have a play around with the label fonts, color-code the branches, collapse the nodes, etc. Hav fun!
  ![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/7064495c-239d-468b-9df3-d449b35777af)


Notice the different species and population structure of the wolves and dogs on the tree. We have more than one clade of dogs embedded within the different wolf populations. Does this suggest that dogs were domesticated from more than one ancient wolf population? Or are these hybridization events? How certain are you about the tree topology?

# References
* Bergström, Anders, et al. "Grey wolf genomic history reveals a dual ancestry of dogs." Nature 607.7918 (2022): 313-320.
* Frantz, Laurent AF, et al. "Genomic and archaeological evidence suggest a dual origin of domestic dogs." Science 352.6290 (2016): 1228-1231.
* Ramos-Madrigal, Jazmín, et al. "Genomes of Pleistocene Siberian wolves uncover multiple extinct wolf lineages." Current Biology 31.1 (2021): 198-206.
# More Resources
* <a href="https://www.popgen.dk/angsd/index.php/Main_Page">ANGSD</a>
* <a href="https://github.com/rambaut/figtree/">Figtree</a>
* <a href="http://www.iqtree.org/doc/Tutorial">IQTREE Tutorial</a>
* <a href="http://www.iqtree.org/doc/">IQTREE Documentation</a>
* <a href="https://www.phylo.org/pdf_docs/jmodeltest-2.1.6-manual.pdf">jModelTest2 Manual</a>
* <a href="https://cme.h-its.org/exelixis/web/software/raxml/">RAxML</a>
* Ho, Simon YW. "The molecular evolutionary clock." Switzerland: Springer Nature, 2021.
* Yang, Ziheng. Molecular evolution: a statistical approach. Oxford University Press, 2014.

