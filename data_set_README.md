## Data for Semantic Grasping with Knowledge Graphs
This dataset has labeled semantic grasps for 190 household objects and 56 tasks

### Files
1. `task1_results.txt` has labeled object-task pairs. Each line follows the format of `${object_name}-${task_name}-${label}`. 
`${label}` can take one of values from `True`, `Weak True`, `False`, `Weak False`.
2. `task2_results.txt` has labeled grasps for object-task pairs that are `True` or `Weak True`. Each line follows the
format of `${object_name}-${grasp_id}-${task_name}:${label}`. `${label}` can take one of values from `-1`, `1`, `0`, `-2`, 
which indicate the grasp is *not suitable*, *suitable*, *unclear*, and *undecided*, respectively. Since we take the majority vote
from 3 turkers, *unclear* means the majority of the turkers thinks the grasp is unclear and *undecided* means there is a tie.
3. `data_specification.py` contains information used by our crowdsource pipeline.
4. `scans/`: stored online. contains point clouds of objects and sampled grasps. 
5. `visualized_grasps/`: stored online.
6. `visualized_labeled_grasps/`: stored online
