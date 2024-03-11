# Introduction to phylogenetics with ancient genomes
In this exercise, we will use IQTREE and jModelTest2 to determine what is the best substitution model to use for our alignment of canid mtDNA. Then, we will use RaXML and IQTREE to build Maximum-Likelihood trees. 
# Selecting the best-fit model
Run this command to determine what the best-fit model is in IQTREE, using the standard model selection operator ```-m TESTONLY```. This model selection test is most similar to other model test programs like jModelTest and ProtTest. 
```
iqtree2 -s wolves_dogs_aln_noindels.phy -m TESTONLY
```
You can view the output of the test in the log file, ```wolves_dogs_aln_noindels.phy.log```. Let's see what the best-fit model is.
```
grep "Best-fit" wolves_dogs_aln_noindels.phy.log
```
According to the IQTREE test, the **Best-fit model: HKY+F+I+G4 chosen according to BIC**. BIC stands for _Bayesian Information Criterion_ (BIC; Schwarz, 1978) which is closely related to the _Akaike Information Criterion_ (also known as AIC). When scoring the fit of a model, the AIC measures the relative amount of information that is lost when that model is used to approximate reality. Consequently, the model with the minimum AIC score is perferred. To calculate the relative plausibility of fifferent models, you use the difference in the AIC scores across the models and this leads to Akaike weights. You start with the model with the highest weight (lowest AIC score), and then determine the plausible set of models by successively adding the next highest weight until a cumulative weight of 0.95 is reached. BIC uses a different theoretical framework, but it is still similar to BIC in that BIC scores and weights are interpreted similarily. The major difference is that BIC generally penalizes complex models and ends up favoring simpler models. In other words, BIC can be preferable because when fitting models, you can end up overfitting the model by adding too many parameters in the aim of increase the likelihood. BIC solves this issue by introducing a greater penalty for more parameters you introduce.  

Let's see what jModeltest2 suggests is the best-fit model for your data. 
```
java -jar /home/ec2-user/Software/jmodltest2/jmodeltest-2.1.10/jModelTest.jar -d example-data/aP6.fas -f -i -g 4 -s 11 -AIC -BIC -a -o jmodeltest_output

```
This will test all 88 models including gamma models with 4 rate categories (```-f -i -g 4 -a``` ), and then perform the model selection using Akaike (```-AIC```) and Bayesian (```-BIC```) criteria, calculating also a model averaged phylogeny (```-a```). The output file name is specified by the operator ```-o```

Here are what the different operators are: 
```-d``` input data file
```-i``` include models with a proportion invariable sites
```-g```  include models with rate variation among sites and number of categories 
```-s``` number of substitution schemes (e.g., -s 11) (it has to be 3,5,7,11,203; default is 3)
```-AIC``` calculate the Akaike Information Criterion
```-BIC```   calculate the Bayesian Information Criterion 
```-a```   estimate model-averaged phylogeny for each active criterion
```-o``` output file name

# Building a Maximum-Likelihood phylgenetic tree with IQTREE
There are many different programs you can use to build a ML tree. One very flexible ML-based method is <a href="http://www.iqtree.org/">IQTREE</a>. IQTREE is great, has extensive documentation, and also integrates model selection with a wide variety of different models, so you don't have to use a separate model tester. 

Now, let's make a tree using this model, with with 1000 bootstrap replicates, which is what the IQTREE manual recommends.  
```
iqtree2 -s wolves_dogs_aln_noindels.phy -m HKY+F+I+G4 -B 1000 -redo
```

Use ```-redo``` option if you really want to redo the analysis and overwrite all output files. (Yes). This will take a few minutes.

When IQTREE is finished running, download ```wolves_dogs_aln_noindels.phy.treefile``` onto your local computer with the command:
```
scp -i apgc-2021-key.pem.txt your_user_name@3.249.84.19:/home/ec2-user/Data/wolves_dogs_aln_noindels.phy.treefile .
```
# Building a Maximum-Likelihood phylogenetic tree with RAxML
As I have previously mentioned, there are many different programs you can use to build a ML tree. <a href="https://cme.h-its.org/exelixis/web/software/raxml/index.html">RAxML</a> is one of the most popular ML-based method in phylogenetics. 
```
raxmlHPC-SSE3 -f a -x -m -p -# 100 -s wolves_dogs_aln_noindels.phy -n raxml_tree -T 1
```
Here are what the different operators are: 

```-f``` selects the algorithm to use. The ```a``` argument selects a rapid bootstrap analysis and best-tree search in one run.

```-x``` random seed for rapid bootstrapping. $RANDOM is a UNIX variable that specifies a random number.

```-p``` random seed for parsimony inferences.

```-#``` number of alternative runs on distinct starting trees. We choose 100 starting trees. 

```-m``` select the model of evolution. ```GTRGAMMA``` specifies the general-time-reversible nucleotide substitution model with a gamma model of rate heterogeneity. Compared to IQTREE, ```GTRGAMMA``` and similar variations of it (e.g. ```GTRCAT```) are really the only substitution models RaXML offers.

```-s``` input alignment file.

```-n``` name of output file.

```-T``` number of threads to use (cores). Let's go with 1. 

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
You can manipulate the tree by zooming in/expanding the branches using the "Layout" panel on the left by adjusting the "Zoom" and "Expansion" toggles.
  ![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/7064495c-239d-468b-9df3-d449b35777af)


Notice the different species and population structure of the wolves and dogs on the tree. We have two clades of dogs embedded within the different wolf populations. Does this suggest that dogs were domesticated from more than one ancient wolf population? Or are these hybridization events?

# References
* Bergström, Anders, et al. "Grey wolf genomic history reveals a dual ancestry of dogs." Nature 607.7918 (2022): 313-320.
* Frantz, Laurent AF, et al. "Genomic and archaeological evidence suggest a dual origin of domestic dogs." Science 352.6290 (2016): 1228-1231.
* Ramos-Madrigal, Jazmín, et al. "Genomes of Pleistocene Siberian wolves uncover multiple extinct wolf lineages." Current Biology 31.1 (2021): 198-206.
# More Resources
* <a href="http://www.iqtree.org/doc/Tutorial">IQTREE Tutorial</a>
* <a href="http://www.iqtree.org/doc/">IQTREE Documentation</a>

