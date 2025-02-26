- #isaaclab
- 虽然技术上你可以将环境代码放在任何地方，只要正确配置 entry_point 和 Python 路径，但 **为了与教程示例保持一致，利用框架提供的自动注册机制，以及保持代码的组织性和可维护性，强烈建议将你的经典相关代码放在 isaaclab_tasks 文件夹下。**
- **注册环境**
  
  在之前的教程中，我们学习了如何创建一个自定义的倒立摆环境。我们通过导入环境类及其配置类来手动创建环境实例。
  
  ```python
  # 创建环境配置
  env_cfg = CartpoleEnvCfg()
  env_cfg.scene.num_envs = args_cli.num_envs
  # 设置 RL 环境
  env = ManagerBasedRLEnv(cfg=env_cfg)
  ```
  
  虽然这种方法很简单直接，但当我们拥有大量环境套件时，它就显得不具备可扩展性。在本教程中，我们将展示如何使用 `gymnasium.register()` 方法将环境注册到 Gymnasium 注册表中。 这允许我们通过 `gymnasium.make()` 函数来创建环境。
  
  **代码**
  
  本教程对应于 `scripts/environments` 目录下的 `random_agent.py` 脚本。
  
  **代码详解**
  
  `envs.ManagerBasedRLEnv` 类继承自 `gymnasium.Env` 类，以遵循标准接口。然而，与传统的 Gym 环境不同，`envs.ManagerBasedRLEnv` 实现了一个向量化环境。这意味着多个环境实例在同一进程中同时运行，并且所有数据都以批处理方式返回。
  
  类似地，`envs.DirectRLEnv` 类也继承自 `gymnasium.Env` 类，用于直接工作流程。 对于 `envs.DirectMARLEnv`，虽然它不继承自 Gymnasium，但可以以相同的方式注册和创建。
  
  **使用 gym 注册表**
  
  要注册环境，我们使用 `gymnasium.register()` 方法。此方法接受环境名称、环境类的入口点以及环境配置类的入口点。
  
  **注意**
  
  Gymnasium 注册表是一个全局注册表。 因此，务必确保环境名称是唯一的。 否则，注册表在注册环境时会抛出错误。
  
  **基于管理器 (Manager-Based) 的环境**
  
  对于基于管理器的环境，以下代码展示了在 `isaaclab_tasks.manager_based.classic.cartpole` 子包中注册倒立摆环境的调用：
  
  ```python
  import gymnasium as gym
  
  from . import agents
  
  ##
  # 注册 Gym 环境。
  ##
  
  gym.register(
      id="Isaac-Cartpole-v0",
      entry_point="isaaclab.envs:ManagerBasedRLEnv",
      disable_env_checker=True,
      kwargs={
          "env_cfg_entry_point": f"{__name__}.cartpole_env_cfg:CartpoleEnvCfg",
          "rl_games_cfg_entry_point": f"{agents.__name__}:rl_games_ppo_cfg.yaml",
          "rsl_rl_cfg_entry_point": f"{agents.__name__}.rsl_rl_ppo_cfg:CartpolePPORunnerCfg",
          "skrl_cfg_entry_point": f"{agents.__name__}:skrl_ppo_cfg.yaml",
          "sb3_cfg_entry_point": f"{agents.__name__}:sb3_ppo_cfg.yaml",
      },
  )
  
  gym.register(
      id="Isaac-Cartpole-RGB-v0",
      entry_point="isaaclab.envs:ManagerBasedRLEnv",
      disable_env_checker=True,
      kwargs={
          "env_cfg_entry_point": f"{__name__}.cartpole_camera_env_cfg:CartpoleRGBCameraEnvCfg",
          "rl_games_cfg_entry_point": f"{agents.__name__}:rl_games_camera_ppo_cfg.yaml",
      },
  )
  
  gym.register(
      id="Isaac-Cartpole-Depth-v0",
      entry_point="isaaclab.envs:ManagerBasedRLEnv",
      disable_env_checker=True,
      kwargs={
          "env_cfg_entry_point": f"{__name__}.cartpole_camera_env_cfg:CartpoleDepthCameraEnvCfg",
          "rl_games_cfg_entry_point": f"{agents.__name__}:rl_games_camera_ppo_cfg.yaml",
      },
  )
  
  gym.register(
      id="Isaac-Cartpole-RGB-ResNet18-v0",
      entry_point="isaaclab.envs:ManagerBasedRLEnv",
      disable_env_checker=True,
      kwargs={
          "env_cfg_entry_point": f"{__name__}.cartpole_camera_env_cfg:CartpoleResNet18CameraEnvCfg",
          "rl_games_cfg_entry_point": f"{agents.__name__}:rl_games_feature_ppo_cfg.yaml",
      },
  )
  
  gym.register(
      id="Isaac-Cartpole-RGB-TheiaTiny-v0",
      entry_point="isaaclab.envs:ManagerBasedRLEnv",
      disable_env_checker=True,
      kwargs={
          "env_cfg_entry_point": f"{__name__}.cartpole_camera_env_cfg:CartpoleTheiaTinyCameraEnvCfg",
          "rl_games_cfg_entry_point": f"{agents.__name__}:rl_games_feature_ppo_cfg.yaml",
      },
  )
  ```
  
  `id` 参数是环境的名称。 作为一种约定，我们将所有环境都以 `Isaac-` 为前缀命名，以便更容易在注册表中搜索它们。 环境的名称通常后跟任务名称，然后是机器人名称。 例如，对于在平坦地形上使用 ANYmal C 进行的腿部运动，环境被称为 `Isaac-Velocity-Flat-Anymal-C-v0`。 版本号 `v<N>` 通常用于指定同一环境的不同变体。 否则，环境的名称可能会变得太长且难以阅读。
  
  `entry_point` 参数是环境类的入口点。 入口点是 `<模块>:<类>` 形式的字符串。 在倒立摆环境的情况下，入口点是 `isaaclab.envs:ManagerBasedRLEnv`。 入口点用于在创建环境实例时导入环境类。
  
  `env_cfg_entry_point` 参数指定环境的默认配置。 默认配置使用 `isaaclab_tasks.utils.parse_env_cfg()` 函数加载。 然后将其传递给 `gymnasium.make()` 函数以创建环境实例。 配置入口点可以是 YAML 文件或 Python 配置类。
  
  **直接环境 (Direct Environments)**
  
  对于基于直接的环境，环境注册遵循类似的模式。 我们不是将环境的入口点注册为 `ManagerBasedRLEnv` 类，而是将环境的入口点注册为环境的实现类。 此外，我们在环境名称中添加后缀 `-Direct`，以将其与基于管理器的环境区分开来。
  
  例如，以下代码展示了在 `isaaclab_tasks.direct.cartpole` 子包中注册倒立摆环境的调用：
  
  ```python
  import gymnasium as gym
  
  from . import agents
  
  ##
  # 注册 Gym 环境。
  ##
  
  gym.register(
      id="Isaac-Cartpole-Direct-v0",
      entry_point=f"{__name__}.cartpole_env:CartpoleEnv",
      disable_env_checker=True,
      kwargs={
          "env_cfg_entry_point": f"{__name__}.cartpole_env:CartpoleEnvCfg",
          "rl_games_cfg_entry_point": f"{agents.__name__}:rl_games_ppo_cfg.yaml",
          "rsl_rl_cfg_entry_point": f"{agents.__name__}.rsl_rl_ppo_cfg:CartpolePPORunnerCfg",
          "skrl_cfg_entry_point": f"{agents.__name__}:skrl_ppo_cfg.yaml",
          "sb3_cfg_entry_point": f"{agents.__name__}:sb3_ppo_cfg.yaml",
      },
  )
  
  gym.register(
  ```
  
  **创建环境**
  
  为了向 gym 注册表告知 `isaaclab_tasks` 扩展提供的所有环境，我们必须在脚本的开头导入该模块。 这将执行 `__init__.py` 文件，该文件迭代所有子包并注册它们各自的环境。
  
  ```python
  import isaaclab_tasks  # noqa: F401
  ```
  
  在本教程中，任务名称是从命令行读取的。 任务名称用于解析默认配置以及创建环境实例。 此外，其他解析的命令行参数（例如环境数量、模拟设备以及是否渲染）用于覆盖默认配置。
  
  ```python
  # 创建环境配置
  env_cfg = parse_env_cfg(
      args_cli.task, device=args_cli.device, num_envs=args_cli.num_envs, use_fabric=not args_cli.disable_fabric
  )
  # 创建环境
  env = gym.make(args_cli.task, cfg=env_cfg)
  ```
  
  一旦创建了环境，其余的执行就遵循标准的重置和步进过程。
-