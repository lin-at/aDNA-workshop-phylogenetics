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
* Open **FigTree**. At the top click on the button that says "Node" and click on the branch for the clade of the 4 coyote sequences so that it is higlighted in purple. Click on the button that says "Reroot  on the top left.
* On the Layout panel on the left, click the little carrot triangle next to "Trees" and check the box  for "Order nodes". Make sure That the "Ordering" pull-down menu is "increasing"
* You can manipulate the tree by zooming in/expanding the branches using the "Layout" panel on the left by adjusting the "Zoom" and "Expansion" toggles.

