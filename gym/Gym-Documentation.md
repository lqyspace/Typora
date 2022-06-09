[TOC]

# Gym Documentation

## Installation

```python
pip install -U gym
or
conda install -n myenv gym
```

## Environments

Gym 基本的构建块是 `Env` 类，这是一个模拟器，他可以模拟出一个环境，让你的智能体在里面进行训练，并且它是一个 `python` 类。Gym里面打包了很多的环境，比如 `MountainCar` 环境。

![image-20220514211625350](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205271310614.png)

```python
import gym
env = gym.make('MountainCar-v0')
```

`Env` 的基本结构由 `observation_space` 和 `action_space` 这两个属性描述。

`observation_space` 定义了结构以及环境的状态的观察值的合法值；观察可以根据不同的环境定义为不同的事物，通用的形式是game的截图，当然也可以被定义为其他的形式，例如：以向量表示的环境的某一个特性。

`action_space` 定义了可以被用于环境的合法的动作。

```python
# Observation and action space
obs_space = env.observation_space
action_space = env.action_space
print("The observation space: {}".format(obs_space))
print("The action space: {}".format(action_space))
```



```python
OUTPUT：
The observation space: Box([-1.2 -0.07], [0.6 0.07], (2,), float32)
The action space: Discrete(3)
```

输出的 `observation` 是一个两个值的向量，第一个位置，第二是速度。



## Interacting with the Environment

`Env` 的两个重要的函数可以帮助 Agent 与 环境 进行交互。

- `reset` :  该函数将环境重设为初始状态，并且根据初始状态返回环境的观察值
- `step` :  该函数将一个 `action` 作为输入并将其应用到环境中，然后将导致环境转移到一个新的状态

`step` 函数返回四个值：`observation` 、 `reward` 、 `done` 、 `info`

- `observation` :  环境状态的观察值
- `reward` :  你从环境中拿到的奖励
- `done` :  `episode` 是否到达终点，如果是，你需要结束模拟器或者将环境重设以重启一个 `episode` 。
- `info`： 提供了一些额外的信息，例如剩余的生命数，或者调试的信息。

```python
import matplotlib.pyplot as plt
# reset the environment and see the initial observation
obs = env.reset()
print('The initial observation is {}'.format(obs))

# Sample a random action from the entire action space
random_action = env.action_space.sample()

# Take the action and get the new observation space
new_obs, reward, done, info = env.step(random_action)
print('The new observation is {}'.format(new_obs))
```

```python
OUTPUT:
The initial observation is [-0.48235664 0.]
The new observation is [-0.48367523 -0.00130853]
```

如果你想查看环境在当前的状态下表现如何，你可使用如下：

```python
env.render(mode='human')
```

这个函数可以以一种弹窗的形式弹出当前状态下的环境，同时你也可使用 `close` 函数关闭弹窗。

```python
env.close()
```



如果你想以一种画面的形式查看游戏的截图，相比较弹窗的形式，你可以将 `render` 函数的 `mode` 参数值设为 `rgb_array` .

```python
env_screen = env.render(mode='rgb_array')
env.close()

import matplotlib.pyplot as plt
plt.imshow(env_screen)
```

![image-20220514222143331](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205271310808.png)

完整的代码如下：

```python
import time

# Number of steps you run the agent for
num_steps = 1500

obs = env.reset()

for step in range(num_steps):
    # take random action, but you can also do something more intelligent
    # action = my_intelligent_agent_fn(obs)
    action = env.action_space.sample()
    
    # apply the action
    obs, reward, done, info = env.step(action)
    
    # Render the env
    env.render()
    
    # Wait a bit before the next frame unless you want to see a crazy fast video
    time.sleep(1)
    
    if done:
        env.reset()
# Close the env
env.close()
```



`Box(n, )` 对应于 n 维的连续空间，在本例中，n=2，因此环境的观察空间是2维空间。我们可以使用观察空间的 `high` 和 `low` 属性来描述环境中 **位置和速度** 的上限和下限。

```python
print("Upper Bound for Env Obervation:", env.observation_space.high)
print("Lower Bound for Env Observation:", env.observation_space.low)
```

```python
OUTPUT:
Upper Bound for Env Observation: [0.6 0.07]
Lower Bound for Env Observation: [-1.2 -0.07]
```

你在创建环境时，可以自定义上限和下限。



`Discrete(n)` 描述了一个 `[0.......n-1]` 的离散空间。在本例中，n = 3，意味着我们的动作值可以取：0、1和2。

==如果你尝试在 step 函数中输入无效的 action，它将会导致错误。==

































