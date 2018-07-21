# reachability_aware_grasping_ws

This is a ros workspace for running the reachability-aware grasping pipeline. It includes scripts for generating binary reachability space, converting to sdf representation and integrating it with the `GraspIt!` grasp planner.

## Requirements
- [ROS Indigo](http://wiki.ros.org/indigo)
- [GraspIt!](https://github.com/graspit-simulator/graspit)
- [Mongo DB!](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/) (Optional: Only needed if you want to run offline computation of reachability space)
- [gitman](https://github.com/jacebrowning/gitman)
- [scikit-fmm](https://pythonhosted.org/scikit-fmm/#)

## Building the workspace
```
git clone http://github.com/CRLab/reachability_aware_grasping_ws.git
gitman install
catkin_make
```

## Online: Pickup Object- grasp planning and trajectory planning
To see the reachability-aware grasping approach in simulation, using our precomputed reachability space for the Fetch robot, run the scripts as described [here] (https://github.com/CRLab/reachability_grasping_demo.git).

To do the offline computation your self, follow steps below.

## Offline: Compute Reachability space and sdf representation

- Define workspace dimension and resolution and upload grid points as tasks to database
```
cd src/reachability_space_generation/scripts/reachability_space_generation
python upload_reachability_tasks.py fetch22.yaml
```

- Compute Inverse Kinematic checks for grid points in the workspace
```
cd src/reachability_space_generation/launch
./launch_fetch_space_generation.sh
```

- Download IK Check result as csv
```
python download_mongo_db.py fetch_to_do fetch_finished fetch_dense_reachability.csv
```

### Generate SDF representation from Binary IK checks
This is done in the reachability_utils package. Copy the binary ik check result from previous step into a data folder in the reachability_utils package.
```
cd src/reachability_utils/scripts/reachability_utils
python process_reachability_data_from_csv.py fetch_dense_reachability.csv
```
The processed sdf data folder can be copied into a config package used for `GraspIt!` reachability_energy_plugin. Processed reachability space for the Fetch robot is available [here])(https://github.com/CRLab/fetch_reachability_config.git)
