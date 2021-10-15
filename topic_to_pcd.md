## ROS点云话题消息转成PCD格式点云
 -  安装pypcd
    ```
    pip install pypcd
    ```
 -  启动转换
    ```
    rosrun pcl_ros bag_to_pcd /home/zsx/projects/2020-07-04-00-07-41.bag /pandar_points pcd
    ```
 -  查看pcd格式点云
	```
	sudo apt install pcl-tools
	pcl_viewer pcd/1593792489.017429000.pcd 
	```
