# rosbag-bible
Notes for .bag files manipulation

## 1. Get information about your .bag file: ##

```sh
time rosbag info my_bagfile_1.bag
```

## 2. **Option 1:** play back the messages immediately and look at the output in multiple terminals ##
  >If you want to extract messages from specific topic or topics:

  > open **terminal_1**:
```sh
roscore
```

  > subscribe to your topics of interest, for example */camera/color/camera_info* and *camera/aligned_depth_to_color/image_raw* echoing (printing) everything         published on this topic, while also teeing it to a file for later review, all in yaml format: 

  > open **terminal_2**:
```sh
rostopic echo /camera/color/camera_info | tee topic1.yaml
```

  > you will get:
```sh
WARNING: topic [/camera/color/camera_info] does not appear to be published yet
```

  > open **terminal_3**:
```sh
rostopic echo camera/aligned_depth_to_color/image_raw | tee topic2.yaml
```

  > you will get:
```sh
WARNING: topic [/camera/aligned_depth_to_color/image_raw] does not appear to be published yet
```
  > Repeat this process for as many topics as you like. Each topic must have its own terminal. 

  > Open up another **terminal_4** to play the bag file. We will now play back the bag file as quickly as possible (using the --immediate option), publishing ONLY the topics of interest. The format is: 

```sh
time rosbag play --immediate demo.bag --topics /topic1 /topic2 /topic3 /topicN
``` 
  > so in our case, it will be:

```sh
time rosbag play --immediate my_bagfile_1.bag --topics /camera/color/image_raw /camera/aligned_depth_to_color/image_raw 
```
  > Done! Now go look at your two terminals which were each subsribed to a topic, and you'll see the output of all messages for each topic type, in YAML format, with a --- line between each message. In your computer will also find a YAML file for each topic that you chose, with all the info inside it. Use a text editor of your choice, preferably with Syntax Highlighting for YAML file types (ex: Sublime Text 3) to view the messages in the files. 

  > Note: If for some reason one of your rostopic processes missed the messages, just kill its process with Ctrl + C, restart it, and call the rosbag play command again. 
--------------------------------------------------------------------------------------------------------
  > **ATTENTION**: There is an **option_2** to get the messages through a python script, follow this link: http://wiki.ros.org/rosbag/Tutorials/reading%20msgs%20from%20a%20bag%20file
  > I followed the option_2 installation procedure from the link above and tried to run:
```sh
 time ros_readbagfile my_bagfile_1.bag /camera/color/image_raw | tee topics.yaml
``` 
  > but i was getting the error: 
```sh
ros_readbagfile: command not found
```
  I managed to solve it, by running first the command (found it here: https://answers.ros.org/question/371583/ros_readbagfile-command-not-found/ and here https://askubuntu.com/questions/402353/how-to-add-home-username-bin-to-path):
```sh
export PATH="$HOME/bin:$PATH"
```
  > and then the:
```sh
 time ros_readbagfile my_bagfile_1.bag /camera/color/image_raw | tee topics.yaml
``` 
  > I am getting messages now but not sure if it is working properly, so i will just leave it here for now and use **option_1 method**.
-----------------------------------------------------------------------------------------------------------
## 3. Use of bagpy library #
  > Documentation link: https://jmscslgroup.github.io/bagpy/bagpy_example.html
  
  > Github link: https://github.com/jmscslgroup/bagpy

Some Impotrant uses of bagpy:

1. Decode message by topic: (potential problem - i have experienced kernel crashes with big messages)

```py
LASER_MSG = b.message_by_topic('/catvehicle/front_laser_points')
LASER_MSG
df_laser = pd.read_csv(LASER_MSG)
df_laser
```

2.Decode all available topics:

```py
csvfiles = []
for t in b.topics:
    data = b.message_by_topic(t)
    csvfiles.append(data)

```
