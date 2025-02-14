- #isaaclab #bug
- 问题：无法从国外服务器下载资产
	- 2025-02-13 09:17:52 [2,119,546ms] [Warning] [omni.usd] Warning: in _ReportErrors at line 2890 of /builds/omniverse/usd-ci/USD/pxr/usd/usd/stage.cpp -- In </World/envs/env_0/Robot/RH_THIGH/collisions>: Could not open asset @http://omniverse-content-production.s3-us-west-2.amazonaws.com/Assets/Isaac/4.5/Isaac/IsaacLab/Robots/ANYbotics/ANYmal-C/Props/instanceable_meshes.usd@ for reference introduced by @http://omniverse-content-production.s3-us-west-2.amazonaws.com/Assets/Isaac/4.5/Isaac/IsaacLab/Robots/ANYbotics/ANYmal-C/anymal_c.usd@</anymal/RH_THIGH/collisions>. (recomposing stage on stage @anon:0x5b38e6a04440:World0.usd@ <0x5b38fac457b0>)
- 解决方法：下载资产并修改资产路径
	- https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_faq.html#isaac-sim-setup-assets-content-pack
	- 首先下载资产文件：https://docs.isaacsim.omniverse.nvidia.com/latest/installation/download.html#isaac-sim-latest-release
	- 对资产文件进行解压
	- Edit the **/home/<username>/isaacsim/apps/isaacsim.exp.base.kit** file and add the settings below:
	- ```
	  [settings]
	  persistent.isaac.asset_root.default = "/home/<username>/isaacsim_assets/Assets/Isaac/4.5"
	  exts."isaacsim.asset.browser".folders = [
	    "/home/<username>/isaacsim_assets/Assets/Isaac/4.5/Isaac/Robots",
	    "/home/<username>/isaacsim_assets/Assets/Isaac/4.5/Isaac/People",
	    "/home/<username>/isaacsim_assets/Assets/Isaac/4.5/Isaac/IsaacLab",
	    "/home/<username>/isaacsim_assets/Assets/Isaac/4.5/Isaac/Props",
	    "/home/<username>/isaacsim_assets/Assets/Isaac/4.5/Isaac/Environments",
	    "/home/<username>/isaacsim_assets/Assets/Isaac/4.5/Isaac/Materials",
	    "/home/<username>/isaacsim_assets/Assets/Isaac/4.5/Isaac/Samples",
	    "/home/<username>/isaacsim_assets/Assets/Isaac/4.5/Isaac/Sensors",
	  ]
	  ```
	-