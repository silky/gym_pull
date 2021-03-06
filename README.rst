gym_pull
******
**Gym Pull is an add-on for OpenAI Gym that allows the automatic downloading of user environments.**

Installation
============

``gym_pull`` should be downloaded through pip with the command: ``pip install gym_pull``

To run the add-on, you need to import gym, and then gym_pull:

.. code:: python

	  import gym
	  import gym_pull

Basics
======

The basic syntax for pulling a user environment is

.. code:: python

	  import gym
	  import gym_pull
	  gym.pull('github_username/github_repo/env_name')

The repo github_username/github_repo must contain a file named ``.openai.yml``
in its top-level folder of its ``master`` branch.

Alternatively, you can

- specify a version to download using ``gym.pull('username/repo/env_name', version=1)``
- download the latest versions of all environments in the repo with ``gym.pull('username/repo/*')``

The downloaded environment will be registered as ``USERNAME/ENV_NAME-vVERSION``. You can then make
the environment using the ``gym.make()`` command.

Listing Environments
======

You can list all installed environments by running ``gym.list()``.

Alternatively, you can view all user environments installed by running
``[env for env in gym.list() where '/' in env]``.

Publish User Environments
======

To publish user environments, you must create an '.openai.yml' file in the top-level folder
of the ``master`` branch of your GitHub repository.

The YAML file syntax is a list of envs, where each object has the following properties:

- **id**: String, required - The environment name
- **version**: Integer, required - The environment version number (starting at 0, and incrementing)
- **entry_point**: String, required - The path to the environment entry point (e.g. env/my_env:MyCoolEnv)
- **commit_ref**: String, required - The commit reference (branch name, tag name, short hash, or long hash) to be checked out when downloading files
- **timestep_limit**: Integer, optional - The maximum number of steps someone can take in your env before it timeouts
- **description**: String, optional - A description of your environment
- **trials**: Integer, optional - The reward threshold will be calculated over this number of trials (default to 100)
- **reward_threshold**: Number, optional - The score needed to successfully solve the environment (defaults to None)
- **kwargs**: Object, optional - Keyword arguments to be passed to your entry point
- **nondeterministic**: Boolean, optional - Indicates if your environment is non-deterministic or not (defaults to False)
- **requirements**: List, required - A list of requirements to be installed through pip when the env is downloaded.
- **files**: List, required - The list of files to be downloaded locally

To permit copying properties across env, you can alternatively wrap your env in an 'env' object, as shown below.

Example
======
.. code:: yaml

	  envs:

        # Primary syntax, where each property is directly defined
	    - id: Acrobot
	      version: 0
	      entry_point: envs:AcrobotEnv
	      commit_ref: master
	      timestep_limit: 500
	      description: |
	        The acrobot system includes two joints and two links, where the joint between the two links is actuated.
	        Initially, the links are hanging downwards, and the goal is to swing the end of the lower link
	        up to a given height.
	      requirements:
	        - gym
	        - numpy
          # & syntax creates an anchor that can be referenced later
	      files: &default_files
	        - envs/__init__.py
	        - envs/acrobot.py
	        - envs/cartpole.py
	        - envs/assets/clockwise.png

        # Alternative syntax, where wrapped inside 'env' object to permit copying properties
	    - env: &cartpole
	        id: CartPole
	        version: 1
	        entry_point: envs:CartPoleEnv
	        commit_ref: master
	        timestep_limit: 500
	        reward_threshold: 475.0
	        requirements:
	          - gym
	          - numpy
            # * syntax references previous anchor
	        files: *default_files

	  # Old versions
	    - env:
	        # Copy all properties from cartpole anchor
	        <<: *cartpole
	        # Overrides copied properties
	        version: 0
	        kwargs: { mode: easy }
	        timestep_limit: 200
	        reward_threshold: 195.0
	        commit_ref: v1
	        nondeterministic: true
