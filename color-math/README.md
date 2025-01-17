![](../images/line3.png)

### Material Color Math

<sub>[previous](../solid-material-iv/README.md#user-content-solid-material-iv) • [home](../README.md#user-content-ue4-intro-to-materials) • [next](../masks/README.md#user-content-masks-opacity--translucent)</sub>

![](../images/line3.png)

Lets look at how we can use basic addition and multiplication to alter colors in a material.  This allows us to make changes without necessarily having to go back to **Substance** or **Photoshop** to adjust colors.

<br>

---


##### `Step 1.`\|`UE5MAT`|:small_blue_diamond:

Now lets look at how **Unreal** handles colors.  In **Photoshop's** color picker we can see four representations of color, but the most important to computer graphics are RGB and Hexadecimal.  We also care about a 4th Alpha channel but we do not have any alphas in this texture so we will deal with it later. All colors are derived from 256 values of Red, Green and Blue.  They are represented in Photoshop by 0 through 255.  White is 255, 255, 255 and black is 0, 0, 0.  In this example we have a bluish color of 51, 153 204.

![red in rgb in photoshop](images/ColorPicker.png)

![](../images/line2.png)

##### `Step 2.`\|`UE5MAT`|:small_blue_diamond: :small_blue_diamond: 

Download [T_MarbleTile_BCH.png](../Assets/T_MarbleTile_BCH.png), [T_MarbleTile_N.png](../Assets/T_MarbleTile_N.png) and [T_MarbleTile_MSRAO.png](../Assets/T_MarbleTile_MSRAO.png). Drag them into the **Textures | Surface** folder.  Make sure the normal map has the correct type of compression.

![download three textures](images/MarbleTileTextures.png)

![](../images/line2.png)

##### `Step 3.`\|`UE5MAT`|:small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Go to **Material | Surfaces** and select **MI_WildGrass** and select **Duplicate**. Call this new material `MI_MarbleTile`.  Duplicate the material ball and title.  Slide it to the right. Change the text in the title to `MI_MarbleTile`.

![create new mi_marbletile material](images/dupeMarble.png)

![](../images/line2.png)

##### `Step 4.`\|`UE5MAT`|:small_blue_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Assign **MI_MarbleTile** to the new material ball.  Open up the material and change the three textures to `T_MarbleTile...`.  Set the **UV Multiplier** to `1` and **UV Angle** of `0.0` degrees. Then turn off **RotateUVs** as we don't need it here.

![assign three marble textures](images/marbleTileSettings.png)

##### `Step 5.`\|`UE5MAT`| :small_orange_diamond:

Open up **MF_BaseTexture** and add a **Constant3Vector** to the node chart under the **Base Color**. ouble click the **Constant 3 Vector** and change red to `1`.  

Now in **Unreal** it represents each channel with a number from `0` to `1`.  So if we want to convert from Photoshop style RGB to Unreal we need to divide the value by `/255`.  So the same representation of pure **Red** in UE4 is `255/255`, `0/255`, `0/255`.  This ends up with `1,0,0`.  So UE5 normalizes each range of each color channel between `0` and `1`.

| R | 255 | 0   | 0   |
|---|-----|-----|-----|
| / | 255 | 255 | 255 |
|   | 1   | 0   | 0   |

Right click on the color and select **Start Previewing Node**.  This allows you to look at the color in different parts of the flowchart.  This is the best way to debug materials.  Notice the red ball is now red!

![add constant three vector](images/const3Vect.png)

##### `Step 6.`\|`UE5MAT`| :small_orange_diamond: :small_blue_diamond:

Now lets add another **Constant 3 Vector**.  Make the second vector solid green with a **G** value of `1.0`. This will allow us to add the two vectors together. Right mouse click and select an **Add** node. We will add the two nodes together. Now how do additions work?  When you add two vectors together (RGB<sub>1</sub> & RGB<sub>2</sub>) it just adds the red channel R<sub>1</sub> + R<sub>2</sub>, green channel G<sub>1</sub> + G<sub>2</sub> and blue channel B<sub>1</sub> + B<sub>2</sub>. Even though the add node has a single pin, it will add up all three channels individually.

![put an add node in material](images/addNode.png)

![](../images/line2.png)

##### `Step 7.`\|`UE5MAT`| :small_orange_diamond: :small_blue_diamond: :small_blue_diamond:

Connect the two **Constant Vector 3** nodes to the **Add**. Hit **Start Previewing Node** on the **Add** node.  Now it adds the two channels together (1,0,0 + 0,1,0) making the resulting color 1, 1, 0.  When you have solid Red and solid Green we get yellow. 

|     | R | G | B |
|-----|---|---|---|
| V3a | 1 | 0 | 0 |
| V3b | 0 | 1 | 0 |
| +   | 1 | 1 | 0 |

![add two channels to yellow](images/yellowAdd.png)

![](../images/line2.png)

##### `Step 8.`\|`UE5MAT`| :small_orange_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Right click and add a **Multiply** node. Connect the two **Constant Vector 3** nodes to the **Multiply**. Hit **Start Previewing Node** on the **Mulitpy** node.  Now it multiplies up all three channelsinstead of adding them.  So the red channel multiplies 1 x 0, the green 0 x 1, and the blue 0 x 0.  This leaves our vector at 0,0,0 or *black*.

|     | R | G | B |
|-----|---|---|---|
| V3a | 1 | 0 | 0 |
| V3b | 0 | 1 | 0 |
|  x  | 0 | 0 | 0 |

![multiple two nodes darker](images/multiplyToBlack.png)

![](../images/line2.png)

##### `Step 9.`\|`UE5MAT`| :small_orange_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Change the red to white make it **R** of `1`, **G** of `1` and **B** of `1`.  Now when you multiply it by green the white gets tinted green.  So multiplication of two numbers between 0 and 1 will always remove detail (or leave it the same).  We can use this to alter the hue and tint textures!

|     | R | G | B |
|-----|---|---|---|
| V3a | 0 | 1 | 0 |
| V3b | 1 | 1 | 1 |
|  x  | 0 | 1 | 0 |

![make darker](images/tintMultiply.png)

![](../images/line2.png)

##### `Step 10.`\|`UE5MAT`| :large_blue_diamond:

OK, lets delete the second vector and addition nodes. Now adjust the color of the **Constant Vector 3** to `1`, `1`, `1` or pure white.  Connect the **Texture Parameter | RGB** output to the **Multiply | A** input.  Connect the output of the **Multiply | B** node into the **Base Color** node in the shader. Now multiplying by 1,1,1 does nothing so it will not affect any of our previous work. So our original texture stays white.

![multiply base color texture by white to tint material](images/addTintNodes.png)

![](../images/line2.png)

##### `Step 11.`\|`UE5MAT`| :large_blue_diamond: :small_blue_diamond: 

Now right click the vector and select **Convert to Parameter**.  Call it `BaseColorTint` and make the **Group** `Base Color` and **Sort Priority** `1`. Double check that the **Base Color** texture node is of **Group** `Base Color` and **Sort Priority** of `0`.

![change base color to parameter](images/convertToBaseColParam.png)


![](../images/line2.png)


##### `Step 12.`\|`UE5MAT`| :large_blue_diamond: :small_blue_diamond: :small_blue_diamond: 

Duplicate the marble material ball and text and move it to the right. Change the text on the title to `MI_MarbleTileGreen`.

![duplicate the material ball and text](images/addAnotherBallText.png)

![](../images/line2.png)

##### `Step 13.`\|`UE5MAT`| :large_blue_diamond: :small_blue_diamond: :small_blue_diamond:  :small_blue_diamond: 

Duplicate **Materials | Surfaces | MI_MarbleTile** and call it `MI_MarbleTileGreen`.

![create MI_GreenMarbleTile and assign to material](images/dupateAssign.png)


![](../images/line2.png)

##### `Step 14.`\|`UE5MAT`| :large_blue_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:  :small_blue_diamond: 

Assign this as to the material on the new material ball you just copied. Now open up **MI_GreenMarbleTile** and change the default **BaseColorTint** from white, to a color such as a light green.

![tint tile to green](images/tintColor.png)

![](../images/line2.png)

##### `Step 15.`\|`UE5MAT`| :large_blue_diamond: :small_orange_diamond: 

Now if we go back to the first material ball the tiling looks very wrong on the ball versus the floor.  Right click on **MI_WildGrass** and select **Duplicate**.  Name it `MI_WildGrassLarge`.  Assign it to the material ball and change the **UV Multiplier** to `3` and **UV Angle** to `0`.

![fix tiling on material on first wild grass](images/newGrassText.png)

![](../images/line2.png)

##### `Step 16.`\|`UE5MAT`| :large_blue_diamond: :small_orange_diamond:   :small_blue_diamond: 

*Press* the <kbd>Play</kbd> button  and look at the three material balls and grass ground.  Make sure you are happy with everything.

https://github.com/LSU-UE5/UE5-Materials/assets/5504953/303a007e-27f0-45a8-965b-50261ba1a147

![](../images/line2.png)

##### `Step 17.`\|`UE5MAT`| :large_blue_diamond: :small_orange_diamond: :small_blue_diamond: :small_blue_diamond:

Now a bit of housecleaning.  It is a good idea after we add or multiply to make sure we don't go below 0 or above 1.  So lets add a **Saturate** node in **MF_Texture** between the **Multiply** and **Output Base Color** node. This will stop the multiplying of two vector resulting in a value above 1 (important to do after adding or multiplying textures).  

![add saturate node after multiply in mf_texture](images/saturateNode.png)

![](../images/line2.png)

##### `Step 18.`\|`UE5MAT`| :large_blue_diamond: :small_orange_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Name and organize all of your room 1 files. Organize your **Outliner** and put all of the static meshes and material instances in the **Room 1** folder. Name them so they match what is in the scene. It is good practice to name objects, so you can quickly find them if you need to make changes in the future.

![name outliner and organize](images/organizeNameRm1.png)

![](../images/line2.png)

##### `Step 19.`\|`UE5MAT`| :large_blue_diamond: :small_orange_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Organize your **Outliner** and put all of the remaining objects in the **Room 2** folder. Name them so they match what is in the scene. 

![name outliner and organize](images/organizeName.png)

![](../images/line2.png)

##### `Step 20.`\|`UE5MAT`| :large_blue_diamond: :large_blue_diamond:

Select the **File | Save All** then press the <kbd>Revision Control</kbd> button and select **Submit Content**.  If you are prompted, select **Check Out** for all items that are not checked out of source control. Update the **Changelist Description** message and with the latest changes. Make sure all the files are correct and press the <kbd>Submit</kbd> button. A confirmation will pop up on the bottom right with a message about a changelist was submitted with a commit number.

![save all and submit to perforce](images/submitP4.png)

![](../images/line2.png)

##### `Step 21.`\|`UE5MAT`| :large_blue_diamond: :large_blue_diamond: :small_blue_diamond:

Sometimes not all files get submitted to Unreal especially for files that don't show up in the editor.  It is good practice one you submit in **Unreal** and quit the game to right click on the top most project folder and select **Reconcile Offline Work...**.

This will either give a message saying ther is nothing to reconcile or bring up a tab.  Make sure that these are **NOT** files in the **Intermediate** and **Saved** folders as these should be ignored from the `.p4ignore`.

If the files are in **Content** or **Configuration** then press the <kbd>Reconcile</kbd> button.  Then submit the changes with a message and press the <kbd>Submit</kbd> button.

![reconcile offline work](images/reconcile.png)

![](../images/line.png)

<!-- <img src="https://via.placeholder.com/1000x100/45D7CA/000000/?text=Next Up - Material Instances"> -->
![next up next tile](images/banner.png)

![](../images/line.png)


| [previous](../solid-material-iv/README.md#user-content-solid-material-iv)| [home](../README.md#user-content-ue4-intro-to-materials) | [next](../masks/README.md#user-content-masks-opacity--translucent)|
|---|---|---|
