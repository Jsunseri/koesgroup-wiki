Gnina serves as the groups primary way to perform docking. There are 2 basic functionalities: Using the Vina scoring function, or scoring with the CNN. This wiki will explain a little extra about the results of running `gnina --help` and show some common use case scenarios

**--Installation Guide--**
* Follow the instructions at https://github.com/gnina/gnina

**--`gnina --help` --**
* `--score_only`  -- only using the scoring function to score the given pose (no docking)
* `--minimize` -- only uses the scoring function to go into the energy minimization subroutine on given poses (no docking)
* `--cnn_scoring` -- use the CNN to guide all of the docking process (relatively slow)
* `--cnn_refinement` -- only use CNN to rescore the poses generated using the Vina scoring function (faster)

**-- Vina scoring function --**
* `gnina -r <receptor structure> -l <ligand structure> --autobox_ligand <structure at the center of search> -o <output file>`
* In output file, the `<minimizedAffinity>` property of the molecule is the Vina score. More negative is better

**-- CNN scoring function --**
* using built-in model: `gnina -r <receptor structure> -l <ligand structure> --autobox_ligand <structure at the center of search> -o <output file> --cnn_scoring --gpu` 
* using different model + weights: `gnina -r <receptor structure> -l <ligand structure> --autobox_ligand <structure at the center of search> -o <output file> --cnn_scoring --gpu --cnn_model <model file> --cnn_weights <weights file>`
* In the output file, the `<CNNscore>` is the score (0-1) assigned to the pose by the CNN, higher is better. `<CNNaffinity>` is the predicted affinity of this particular pose to the receptor structure. Higher is better. `<minimizedAffinity>` is the score provided by AutoDock Vina, lower is better.