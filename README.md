![teaser image](https://i.imgur.com/feoihA2.png)

# Instructions for running the Blenderbot demo
*Note: For the other demo with DialogFlow integration, visit the `dialogflow_demo` branch.*

*The source code of the Unity project is available [from this link](https://drive.google.com/file/d/1WTbpeuHm_i0qhJOFfMXjoqwnOtJKihym/view?usp=sharing), but below we provide a stand-alone executable version that doesn't require installing Unity.*
## Preliminaries
- Clone the repository
  ```
  git clone git@github.com:nagyrajmund/gesticulating_agent_unity.git
  cd gesticulating_agent_unity
 
  git submodule init
  git submodule update
  ```
- Download the compiled Unity project:
  - [Linux release](https://drive.google.com/file/d/1DiB-nebwuzdIa5delYZK7J-cMlUhPMeX/view?usp=sharing)
  - Windows 10 release

- Download Apache ActiveMQ 5 [from this link](http://activemq.apache.org/components/classic/download/)

## Installation
### With anaconda (option 1)
- Create a new conda environment and install the requirements (using the terminal on Linux or the Anaconda Prompt on Windows)
  ```
  # From the root of the repository:
  # Install the gesture generation model
  conda create --name gesturebot -y python=3.7
  conda activate gesturebot
  cd gesticulator
  python install_script.py
  
  # Install Mozilla TTS
  cd gesticulator/interface/TTS_repo
  pip install -r requirements.txt
  python setup.py develop
  sudo apt-get install espeak
  
  # Download TTS model files
  gdown --id 1NFsfhH8W8AgcfJ-BsL8CYAwQfZ5k4T-n -O tts_model.pth.tar
  gdown --id 1IAROF3yy9qTK43vG_-R67y3Py9yYbD6t -O config.json
  gdown --id 1Ty5DZdOc0F7OTGj9oJThYbL5iVu_2G0K -O vocoder_model.pth.tar
  gdown --id 1Rd0R_nRCrbjEdpOwq6XwZAktvugiBvmu -O config_vocoder.json
  gdown --id 11oY3Tv0kQtxK_JPgxrfesa99maVXHNxU -O scale_stats_vocoder.npy

  cd ..
  ```

### With docker (option 2)
- Pull the docker image of the gesture generation model
  ```
  docker pull rajmundn/gesticulating_agent:gesturebot_blenderbot
  ```

## Running the project
* Note that the gesture generation model will download around 10 GBs of data (for the language model) into the `.word_vectors_cache` folder when it's run for the first time. However, the 6,6 GB `wiki.en.vec` file can be removed after the first run.

1. Start the ActiveMQ server by running `./bin/activemq start` in a terminal (on Linux) or `bin/activemq start` in a command prompt (on Windows).

2. (option 1): Start running the gesture generation model with conda
  - in the previously created conda environment, from the `gesticulator` folder, run:
    ```
    cd gesticulator/interface
    python main.py
    ```
2. (option 2): Start running the gesture generation model with docker
  - When the project is run for the first time, the docker container may be created by running the following command from the root of the repository:
    
    **replace `PATH_TO_UNITY_DATA` with the absolute path of the `gesturebot_Data` folder in the compiled Unity project**
    ```
    docker run -v PATH_TO_UNITY_DATA:/workspace/gesticulator/interface/docker_volume --network host -ti --name gesturebot_bb rajmundn/gesticulating_agent:gesturebot_blenderbot
    ```
  - After the container has been created, it can be ran with:
    ```
    docker start -ai gesturebot_bb
    ```

3. Run the executable in the compiled Unity project to start the Unity player

Now you should be able to talk with the agent via the following ways:
  - Enter text in the input field and press `Submit` OR
  - Click `Talk` to start recording speech input, and `Stop` to stop recording OR
  - Press `t`-key once to start recording speech input, and `t`-key again to stop recording

and the agent should be moving when it replies.
