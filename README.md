# Obj_segmentation.cpp

## Main parameters 
All the default values  are defined in the "srv_manager.h" file .
**Convention used** : 
* **Default Value** : DEFAULT_INPUT_PARAM_Name.

* **Variable name**:  inputName

**Example** 

* **Name**: RawCloudTopic

* **Default Value**: DEFAULT_INPUT_PARAM_RAW_CLOUD_TOPIC

* **Variable Name**: inputRawCloudTopic

Index |Name |Default | Description|
---  | --- | --- | --- |
1|RawCloudTopic |"/camera/depth/points"|Subscribe to this topic to collect kinect depth points|
2|ShowOriginalCloud|false|Decide whether visualize the original point Clouds|
3|ShowSupportClouds|false|Decide whether visualize the support clouds|
4|ShowClusterClouds|false|Decide whether visualize the cluster clouds|
5|ShowObjectOnSupport|false|Decide whether visualize the object on support clouds|
6|CentroidLogFilePath|" "|Path in which save the file |

## ROS parameters
All the default values and the ros name are defined in the "srv_manager.h" file .
** Convention used** :

* **Ros Name**:  NAME_PARAM_Name

* **Default Value** : DEFAULT_PARAM_Name

* **Variable name**:  inputName

**Example** 

* **Name**: CloudReferenceFrame

* **Ros Name** : NAME_PARAM_INPUT_CLOUD_REFERENCE_FRAME

* **Default Value**: DEFAULT_PARAM_INPUT_CLOUD_REFERENCE_FRAME 

* **Variable Name**: inputCloudReferenceFrame

In order to automatically set the default values the user can set all the parameters to "-1" if they are either float or int. In case of string parameter they have to be set to " . " .

 Name  |Launch Name |Default | Description|
---  | --- |--- | --- |
CloudReferenceFrame |"/pitt/ref_frame/input_cloud" |"/camera_depth_optical_frame"|Reference frame for the input clouds, used to obtain the transformation between the camera and the desired frame  |
outputCloudReferenceFrame|"/pitt/ref_frame/output_cloud"|"/world"|Reference frame for the input clouds, used to obtain the transformation between the camera and the desired frame |
srvDeepFilterDepthThreshold|"/pitt/service/deep_filter/z_threshold"|3.000f|Parameter for the request of the deepfilter service, the service will get rid of the point farther than this threshold along the z axis|
 srvArmFilterForearmMinBox|"/pitt/srv/arm_filter/min_forearm_box"|-0.040f, -0.120f, -0.190f|The minimum coordinates for the forearm bounding box w.r.t. the input (left/right) forearm frame |
 srvArmFilterForearmMaxBox|"/pitt/srv/arm_filter/max_forearm_box"|0.340,  0.120,  0.105|The maximum coordinates for the forearm bounding box w.r.t. the input (left/right) forearm frame|
 srvArmFilterElbowMinBox|"/pitt/srv/arm_filter/min_elbow_box"|-0.090, -0.135, -0.160|The minimum coordinates for the elbow bounding box w.r.t. the input (left/right) elbow frame|
srvArmFilterElbowMaxBox|"/pitt/srv/arm_filter/max_elbow_box"|0.440,  0.135,  0.110|The maximum coordinates for the elbow bounding box w.r.t. the input (left/right) elbow frame|
srvSupportMinIterativeCloudPercentage|"/pitt/srv/supports_segmentation/min_iter_cloud_percent"|0.030f|Minimum percentage to stop looking for supports|
srvSupportMinIterativeSupportPercentage|"/pitt/srv/supports_segmentation/min_iter_support_percent"|0.030f|Minimum number of found planes to stop looking for support|
srvSupportHorizzontalVarianceTreshold|"/pitt/srv/supports_segmentation/horizontal_variance_th"|0.09f|the value(cross product) used to discriminate whether a support is parallel to the ground|
 srvSupportRansacInShapeDistancePointThreshold|"/pitt/srv/supports_segmentation/in_shape_distance_th"|0.02f|RANSAC parameter, distance between points (in meters) to  consider them belonging to the same shape|
srvSupportRansacMaxIterationThreshold|"/pitt/srv/supports_segmentation/max_iter"|10|RANSAC parameter maximum number of iterations for plane segmentation on support service|
srvSupportHorizontalAxis|"/pitt/srv/supports_segmentation/horizontal_axis"|{ 0.0f, 0.0f, -1.0f}|Axis used to check whether the plane is horizontal|
srvSupportEdgeRemoveOffset|"/pitt/srv/supports_segmentation/edge_remove_offset"|{ 0.02, 0.02, 0.005}|Parameter used to remove some space at the edges of the table in order not to consider objects attached to it (for instance walls)|


## Subscription 

Topic  |Callback |Msg | Description|
---  | --- |--- | --- |
RawCloudTopic|depthAcquisition|PointCloud2Ptr|Kinect PointCloud to be processed|

## Publication
Topic  |Msg | Description|
---  | --- |--- | 
TOPIC_OUT_NAME_OBJECT_PERCEPTION (defined in the "srv_manager.h")|ClustersOutput|The clusters of the point clouds are published in this node. It is used to comunicate with the other nodes (geometric tracker)

