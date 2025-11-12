# get_tf_eyeoffhand

本目录包含“眼在手外”（eye-on-hand-out）相关的脚本，用于通过 ArUco 标签与机器人末端执行器配合完成相机到机器人底座的标定（手眼标定 / hand-eye calibration），并可视化与保存标定结果。

目录结构
- arucotag/ - ArUco 标签相关的配置与渲染选项
	- arucotag.yaml  - 标签检测与相机相关配置
	- render_option.json - Open3D 可视化视角配置
- get_biaodingpoise.py - 使用 3D 相机进行 ArUco 检测并发布标签位姿（ROS2 节点）
- get_cam2base.py - 从机器人采集末端位姿与标签位姿，执行 hand-eye 标定并保存结果
- arucotag_visualizer.py - 可视化工具（基于 Open3D）
- arucotag_pose_adjust.py - 位姿调整/滤波工具

主要功能
- 实时检测 ArUco 标签并估计位姿（`get_biaodingpoise.py`）
- 收集机器人末端位姿与相机观测到的标签位姿，执行手眼标定（`get_cam2base.py`）
- 可视化点云、相机与标签位姿（Open3D）



```bash
python3 get_biaodingpoise.py
```

该脚本会：
- 打开相机（使用 `kyle_robot_toolbox.camera.Gemini335`）
- 检测 ArUco 标签并可视化（Open3D 窗口）
- 发布 ROS2 话题 `/tag_pose`（String）和 `/tag_trans_mat`（Float32MultiArray）供 `get_cam2base.py` 订阅

1. 运行手眼标定采集与保存

在运行 `get_cam2base.py` 前，先启动 `get_biaodingpoise.py`（或其他发布者），确保 ROS2 话题 `/tag_trans_mat` 有数据。

```bash
python3 get_cam2base.py
```

按脚本提示输入采集次数，程序会在每次采集时移动机器人到指定位置并等待标签位姿数据。标定完成后，结果会被保存到项目内：

- 保存目录：`<project_root>/data/calibration/`（如果不存在会自动创建）
- 文件名：`camera2base_YYYYmmdd_HHMMSS.json`（时间戳命名，避免覆盖）



