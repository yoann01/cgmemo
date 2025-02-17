
# Python Scripts

## Create animated camera from multiple cameras in Maya (Python)

!!! info
    This script creates a single animated camera from selected cameras. In order to set the keyframes to correct frames, the cameras need to have imagePlane node that has an image file containing the frame number. The image file needs to be in format “image.####.ext”. You will also need to change the file_ext variable string to the file extension that the image sequence is using. This is so that the script can substring out the frame number correctly.

Logic around the script:

- [x] List selected nodes

- [x] create a new camera and store its shape and transform node names

- [x] for each camera in selection

    >- read transform and camera attributes to variables
    >- set these attributes to the newly created camera
    >- read frame number from the image file in the image plane of the camera
    >- create a key at this frame to all the attributes

```py
import maya.cmds as cmds

file_ext = ".jpg"

selected = cmds.ls( sl=True )

new_cam = cmds.camera( n='anim_cam')
cameraShape = new_cam[1]
cameraXform = new_cam[0]
print(cameraShape)
print(cameraXform)


for node in selected:
    pos = cmds.camera(node, q=True, p=True)
    rot = cmds.camera(node, q=True, rot=True)
    focal_len = cmds.camera(node, q=True, fl=True)
    vfilm_apert = cmds.camera(node, q=True, vfa=True)
    hfilm_apert = cmds.camera(node, q=True, hfa=True)
   
    relatives = cmds.listRelatives( node )
    imagefile = cmds.getAttr ( relatives[0] + '.imageName' )
    frame = imagefile[(-4-len(file_ext)):(len(file_ext)*-1)]
    print(frame)
   
   
    cmds.camera(cameraShape, e=True, p=pos )
    cmds.camera(cameraShape, e=True, rot=rot )
    cmds.camera(cameraShape, e=True, fl=focal_len )
    cmds.camera(cameraShape, e=True, vfa=vfilm_apert )
    cmds.camera(cameraShape, e=True, hfa=hfilm_apert )
   
    cmds.setKeyframe( cameraXform, t=frame )
    cmds.setKeyframe( cameraShape, t=frame )

```
[www.thebrainextension.com](https://www.thebrainextension.com/houdini-scripts/create-animated-camera-from-multiple-cameras-in-maya-python)

## Set all image plane images to a new path in Maya

!!! info
    You want to grade or resize image plane images and write them to a new location with Nuke. To set all the file paths on the scene to the new path run this script. The images need to be in an image sequence. Replace new_path variable string with the file path pointing to the first image of the new image sequence. Replace file_ext variable string with the file extension of the new image sequence.

Logic around the script:

- [x]  Get a list of all the image plane shape nodes in the scene with ls command with flag type=“imagePlane”

- [x]  for each node in the list

    >- get image name attribute to a variable
    >- substring out the frame
    >- construct a new file path from the prefix, frame, and file_ext variables
    >- set image plane image name attribute to the final_path

```py
import maya.cmds as cmds

new_path = r"new_file_path\test_imageplane_newpath.1001.jpg"
file_ext = ".jpg"

prefix = new_path[0:(-4-len(file_ext))]

nodes = cmds.ls( type='imagePlane' )

for node in nodes:
    image_name = cmds.getAttr ( node + '.imageName' )
    frame = image_name[(-4-len(file_ext)):(len(file_ext)*-1)]
    final_path = prefix + frame + file_ext
    cmds.setAttr( node + '.imageName', final_path, type='string')

```

[www.thebrainextension.com](https://www.thebrainextension.com/houdini-scripts/set-all-image-plane-images-to-a-new-path-in-maya-python)
