# ycl ������Լ�¼

*****************************

## Index

0. [Overview](#overview)
1. [Codes Review](#codes-review) \
   [1.1 Replay Memory](#replay-memory) \
   [1.2 `algorithm.td3.py`](#algorithm_td3_py) \
   [1.3 `TD3.train.py :: main ( )`](#1_1_td3_train_py_main) \
   [1.4 `TD3.agent.py`](#td3_agent_py) \
   [1.5 run_train_episode](#run_train_episode)
9. [���ڰ�װ����](#first_try)


## 0 Overview

### 0.1 Future processing



[Back to index](#index)


## 1 Codes Review

`algorithm.td3.py` �е� `_critic_learn` չ�����㷨�Ĺؼ����̣�
�� `target_Q1`, `target_Q2)` ȡ������Сֵ��

### 1.1 Replay Memory

<!-- https://pytorch.org/tutorials/intermediate/reinforcement_q_learning.html -->

We��ll be using experience replay memory for training implementation. It stores the transitions that 
the agent observes, allowing us to reuse this data later. By sampling from it randomly, the 
transitions that build up a batch are decorrelated. It has been shown that this greatly stabilizes 
and improves the rl training procedure.

For this, we��re going to need two classses:

- Transition - a named tuple representing a single transition in the environment. It essentially maps 
(state, action) pairs to their (next_state, reward) result, with the state being described later on.
- ReplayMemory - a cyclic buffer of bounded size that holds the transitions observed recently. It also 
implements a `.sample()` method for selecting a random batch of transitions for training.

```python
Transition = namedtuple('Transition',
                        ('state', 'action', 'next_state', 'reward'))

class ReplayMemory(object):

    def __init__(self, capacity):
        self.memory = deque([],maxlen=capacity)

    def push(self, *args):
        """Save a transition"""
        self.memory.append(Transition(*args))

    def sample(self, batch_size):
        return random.sample(self.memory, batch_size)

    def __len__(self):
        return len(self.memory)
```


[Back to index](#index)


### 1.2 `algorithm.td3.py` <span id="algorithm_td3_py"></span>

�㷨�� TD3 �Ķ���, �����У�
- `predict(self, obs)`
- `learn(self, obs, action, reward, next_obs, terminal)`
- `_critic_learn(self, obs, action, reward, next_obs, terminal)`
- `_actor_learn(self, obs)`
- `sync_target(self)`
- `save_model(self,env,version,episode)`

```python
    def _critic_learn(self, obs, action, reward, next_obs, terminal):
        with torch.no_grad():
            noise=torch.normal(0, self.policy_noise, size=action.size()).clamp(
                -self.noise_clip, self.noise_clip).to(self.device)

            next_action = (self.target_model.policy(next_obs) + noise).clamp(
                -1, 1)
            target_Q1, target_Q2 = self.target_model.value(
                next_obs, next_action)
            target_Q = torch.minimum(target_Q1, target_Q2)  # ȡ������Сֵ

            target_Q = reward + (1 - terminal) * self.gamma * target_Q

        cur_Q1, cur_Q2 = self.model.value(obs, action)
        critic_loss = F.mse_loss(cur_Q1, target_Q) + F.mse_loss(cur_Q2, target_Q)
        self.critic_optimizer.zero_grad()
        critic_loss.backward()
        self.critic_optimizer.step()
```


[Back to index](#index)


### 1.3 `TD3.train.py :: main ( )` <span id="1_1_td3_train_py_main"></span>

**�˴��Ĵ�������`github.com/sfujim/TD3`���Ժ����Աȷ�����**

���Ǹ�ȫ�ֺ�����ֱ����
```python
if __name__ == "__main__":
    args = parser.parse_args()
    main()
```
�б����ã�����ǰ�������˲���������

create key objects��
- ���ɻ��� `env = gym.make (...)`
- ����ģ�� `model = Model (...)`, sfujim �����н� model ���� policy ��
- �����㷨 `algorithm = TD3 (...)`, sfujim ������ policy = TD3.TD3(**kwargs), �� policy ��Ӧ algorithm
- ���� agent `agent = Agent (...)`
- ���ɻط��ڴ� `rpm = RelayMemory (...)`

key methods:
- ȫ�ֺ��� `run_train_episode (...)`��sfujim ����������ʹ�� policy.train (replay_buffer, batchsize)
- ȫ�ֺ��� `run_evaluate_episodes`��sfujim ����������ʹ�� eval_policy(policy, args.env, args.seed)
����ʹ�ã�
```python
	# Evaluate untrained policy
	evaluations = [eval_policy(policy, args.env, args.seed)]
```
Ȼ��
```python
		# Evaluate episode
		if (t + 1) % args.eval_freq == 0:
			evaluations.append(eval_policy(policy, args.env, args.seed))
			np.save(f"./results/{file_name}", evaluations)
			if args.save_model: policy.save(f"./models/{file_name}")
```
���� `evaluations` ��һ�� `list`, �� `evaluations.append` ��������������list�С�

[Back to index](#index)


### 1.4 `TD3.agent.py` <span id="td3_agent_py"></span>

������ Agent���䷽���У�
- `predict(self, obs)`
- `sample(self, obs)`
- `learn(self, obs, action, reward, next_obs, terminal)`
- `save_model(self,env,version,episode)`


[Back to index](#index)


### 1.5 run_train_episode

```python
# Run episode for training
def run_train_episode(agent, env, rpm):
    episode_reward = 0
    episode_steps = 0

    while not done:
        episode_steps += 1

        # Select action randomly or according to policy
        if rpm.size() < WARMUP_STEPS:
            action = np.random.uniform(-1, 1, size=action_dim)
        else:
            action = agent.sample(obs)

        # Perform action
        next_obs, reward, done, _ = env.step(action)
        terminal = float(done) if episode_steps < env._max_episode_steps else 0

        # Store data in replay memory

        # Train agent after collecting sufficient data
        if rpm.size() >= WARMUP_STEPS:
            batch_obs, batch_action, batch_reward, batch_next_obs, batch_terminal = rpm.sample_batch(
                BATCH_SIZE)
            agent.learn(batch_obs, batch_action, batch_reward, batch_next_obs,
                        batch_terminal)

    return episode_reward, episode_steps
```

������ sfujim �е� `policy.train (replay_buffer, batchsize)` ���жԱ�


[Back to index](#index)


## 9 ���ڰ�װ���� <span id="first_try"></span>

�����İ�װ�μ� vsMem/coding/python/pytorch.md �� gym.md, python_env.md �� python.md

debug TD3 �е� train
1. ���ִ��� ��No module named 'termcolor'�� \
    ����λ��: utils/logger.py ln6 - from termcolor import colored  
```shell
(env_pytorch) D:\mygit\gym>pip show termcolor
WARNING: Package(s) not found: termcolor
```

��װ termcolor:
```shell
(env_pytorch) D:\codes\ycl\2022_06_10\graduation project>pip install termcolor
Collecting termcolor
  Downloading termcolor-1.1.0.tar.gz (3.9 kB)
  Preparing metadata (setup.py) ... done
Building wheels for collected packages: termcolor
  Building wheel for termcolor (setup.py) ... done
  Created wheel for termcolor: filename=termcolor-1.1.0-py3-none-any.whl size=4832 sha256=e96204805d8c4b94595ecf79187c0722b04fa5d16b1d65b2b42f88382b45fc03
  Stored in directory: c:\users\jiche\appdata\local\pip\cache\wheels\b6\0d\90\0d1bbd99855f99cb2f6c2e5ff96f8023fad8ec367695f7d72d
Successfully built termcolor
Installing collected packages: termcolor
Successfully installed termcolor-1.1.0
```

��װ��ɺ���
```shell
(env_pytorch) D:\codes\ycl\2022_06_10\graduation project>pip show termcolor
Name: termcolor
Version: 1.1.0
Summary: ANSII Color formatting for output in terminal.
Home-page: http://pypi.python.org/pypi/termcolor
Author: Konstantin Lepa
Author-email: konstantin.lepa@gmail.com
License: MIT
Location: c:\users\jiche\.conda\envs\env_pytorch\lib\site-packages
Requires:
Required-by:
```


2. ���ִ��� ��No module named 'numba'�� \
    ����λ��: utils/replay_memory.py ln3 - from numba import jit  
```shell
(env_pytorch) D:\codes\ycl\2022_06_10\graduation project>pip show numba
WARNING: Package(s) not found: numba
```

��װ numba:
```batch
(env_pytorch) D:\codes\ycl\2022_06_10\graduation project>pip install numba
Collecting numba
  Downloading numba-0.55.2-cp39-cp39-win_amd64.whl (2.4 MB)
     ---------------------------------------- 2.4/2.4 MB 211.5 kB/s eta 0:00:00
Requirement already satisfied: setuptools in c:\users\jiche\.conda\envs\env_pytorch\lib\site-packages (from numba) (62.3.3)
Requirement already satisfied: numpy<1.23,>=1.18 in c:\users\jiche\.conda\envs\env_pytorch\lib\site-packages (from numba) (1.22.4)
Collecting llvmlite<0.39,>=0.38.0rc1
  Downloading llvmlite-0.38.1-cp39-cp39-win_amd64.whl (23.2 MB)
     ---------------------------------------- 23.2/23.2 MB 982.2 kB/s eta 0:00:00
Installing collected packages: llvmlite, numba
Successfully installed llvmlite-0.38.1 numba-0.55.2
```

��װ��ɺ���
```batch
(env_pytorch) D:\codes\ycl\2022_06_10\graduation project>pip show numba
Name: numba
Version: 0.55.2
Summary: compiling Python code using LLVM
Home-page: https://numba.pydata.org
Author:
Author-email:
License: BSD
Location: c:\users\jiche\.conda\envs\env_pytorch\lib\site-packages
Requires: llvmlite, numpy, setuptools
Required-by:
```

3. ����ǰ�氲װ tensorboard


[Back to index](#index)




