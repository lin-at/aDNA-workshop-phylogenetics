# Introduction to phylogenetics with ancient genomes

* Run this command to determine what the best-fit model is, specifically the standard model selection (like jModelTest, ProtTest)
```
iqtree2 -s wolves_dogs_aln_noindels.phy -m TESTONLY
```

Best-fit model: HKY+F+I+G4 chosen according to BIC

* If you now look at ```wolves_dogs_aln_noindels.phy.treefile``` you will see that IQ-TREE selected HKY+F+I+G4 as the best-fit model for this example data. 
* Thus, for additional analyses you do not have to perform the model test again and can use the selected model.
* Use ```-redo``` option if you really want to redo the analysis and overwrite all output files. (Yes)

```
iqtree2 -s wolves_dogs_aln_noindels.phy -m HKY+F+I+G4 -B 1000 -redo
```


* Download ```wolves_dogs_aln_noindels.phy.treefile```
* Open **FigTree**.
* Enter "bootstrap" (sorry for my typo!) in the pop-up window.
    ![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/7f633e10-b5eb-493a-ba11-a554e325c8ec)
* At the top click on the button that says "Node" and click on the branch for the clade of the 4 coyote sequences so that it is higlighted in purple. These are the outgroup sequences that roots the topology of the tree.
![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/44e459e7-22f6-4a08-9e13-fd2480f2ac89)
* Click on the button that says "Reroot  on the top left.
* On the Layout panel on the left, click the little carrot triangle next to "Trees" and check the box  for "Order nodes". On the "Ordering" pull-down menu, select "increasing". This is just to order the tree branches in a more visually organized way.
![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/a322088d-634d-41e8-914a-40fdd51ce72f)
* On the Layout panel on the left, click the little carrot triangle next to "Node Labels" and check the box. Select "bootstrap" on the "Display" pull-down menu. 
 ![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/5ee4bf4c-a7d9-4db7-9c0b-c2dc81d2ad70)
* You can manipulate the tree by zooming in/expanding the branches using the "Layout" panel on the left by adjusting the "Zoom" and "Expansion" toggles.
  ![image](https://github.com/lin-at/aDNA-workshop-phylogenetics/assets/68337277/ab4598c0-2450-4b41-a273-df12aefa6e84)

Notice the different species and population structure of the wolves and dogs on the tree. We have two clades of dogs embedded within the different wolf populations. Does this suggest that dogs were domesticated from more than one ancient wolf population? Or are these hybridization events?

# References
* Bergström, Anders, et al. "Grey wolf genomic history reveals a dual ancestry of dogs." Nature 607.7918 (2022): 313-320.
* Frantz, Laurent AF, et al. "Genomic and archaeological evidence suggest a dual origin of domestic dogs." Science 352.6290 (2016): 1228-1231.
* Ramos-Madrigal, Jazmín, et al. "Genomes of Pleistocene Siberian wolves uncover multiple extinct wolf lineages." Current Biology 31.1 (2021): 198-206.

