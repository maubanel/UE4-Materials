![](../images/line3.png)

### World Aligned Materials

<sub>[previous](../export-textures/README.md#user-content-export-textures) • [home](../README.md#user-content-ue5-intro-to-materials)

![](../images/line3.png)

What happens if I want a long stretch of brick wall that follows different shaped and placed geometries.  It would take a lot of painstaking tweaking to align all the UV's.  There is a better way in unreal to use world coordinates to place the UVs as opposed to locally with the model.

<br>

---


##### `Step 1.`\|`UE5MAT`|:small_blue_diamond:

Open up the UE5 Editor and drag the three brick textures you made in the last module into the **Textures | Surfaces** folder.

![drag three brick wall textures into surfaces folder](images/importNewBrickFiles.png)

![](../images/line2.png)

##### `Step 2.`\|`UE5MAT`|:small_blue_diamond: :small_blue_diamond: 

Create a new material function called **MF_WorldAligned_MSRAO**.  Open up the new material function and add a **Texture Object Parameter** node,

![add three texture object parameters](images/newMatThreeTextObj.png)

![](../images/line2.png)

##### `Step 3.`\|`UE5MAT`|:small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Name the first **Texture Object Parameter** `Base Color`.  Assign  `T_Base_BCH` to the **Texture** slot.  Change the **Group** to `WorldAligned` and **Sort Priority** of `0`.

Add another **Texture Object Parameter** `Normal`.  Assign  `T_Base_N` to the **Texture** slot.  Change the **Group** to `WorldAligned` and **Sort Priority** of `1`.

Name the first **Texture Object Parameter** `MSRAO`.  Assign  `T_Base_MSRAO` to the **Texture** slot.  Change the **Group** to `WorldAligned` and **Sort Priority** of `2`.


![add three texture object parameters](images/threeTextObj.png)

![](../images/line2.png)

##### `Step 4.`\|`UE5MAT`|:small_blue_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Add twp **WorldAlignedTexture** node to the graph.  This will adjust the UVs so the textures are in world space. Connect the output pins of the **BaseColor** and **MSRAO** nodes to the top pin in the **WorldAlignedTexture** node.

Normal maps are different so add a **WorldAlignedNormal** node for the normal maps.  Connect the output of the **Normal** objeect to the **Texture Object** pin of the **WorldAlignedNormal** node.

![add worldaligned textures node](images/baseTexturesAlgined.png)

![](../images/line2.png)

##### `Step 5.`\|`UE5MAT`| :small_orange_diamond:

Now we have an RGBA texture in our MSRAO.  Now the output pins just carry three values (XYZ Textue).  We need to add a **Static Bool** node and set it to `true`.  Plug it inot the **Export Float 4** node of the **World Aligned Texture** node used for the **MSRAO**.

![turn on export float 4](images/vector4Bool.png)

![](../images/line2.png)

##### `Step 6.`\|`UE5MAT`| :small_orange_diamond: :small_blue_diamond:

Change the output node to `BaseColor`.  Leave the **Sort Priority** at `0`. Connect the **Base Color | World Aligned Texture | XYZ Texture** to **Output BaseColor** node. 

![turn on export float 4](images/baseColorOutput.png)

![](../images/line2.png)

##### `Step 7.`\|`UE5MAT`| :small_orange_diamond: :small_blue_diamond: :small_blue_diamond:

Add another **Function Output** node called `Normal` and make it **Sort Priority** of `1`. Take the output of the **World Aligned Normal | XYZ Texture** node to the **Normal** pin.  

Pull out from the **XYZ Texture** pin from the **MSRAO | World Aligned Texture** pin.  Add a **Break Out Float 4 Components** node. We need to have four outputs.  Create 4 new **Function Outputs**.

The first one is called `Metallic` with a **Sort Priority** of `2`. Connect the  **Break Out Float 4 Components | R** component to it.

The second one is called `Specular` with a **Sort Priority** of `3`. Connect the  **Break Out Float 4 Components | G** component to it.

The third one is called `Roughness` with a **Sort Priority** of `4`. Connect the  **Break Out Float 4 Components | B** component to it.

The last one is called `Ambient Occlusion` with a **Sort Priority** of `5`. Connect the  **Break Out Float 4 Components | A** component to it.

![turn on export float 4](images/threeOtherOutputs.png)

![](../images/line2.png)

##### `Step 8.`\|`UE5MAT`| :small_orange_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Now we want to adjust the scale of the textures symmetrically.  The input pin for **Texture Size** askes for a **V3** (Three floats). So we need a single float with the same value in all three positions.  Create a **Scalar Parameter** and call it `Texture Size`.  Take the output and add an **Append Many** node.  This is now a **V3**.  Take the **TextureSize** and put it into the **R**, **G**, **B** pins of the **Append Many** node. Take the **Append Many** output node the three **Texture Size** nodes in the 3 world aligned nodes.

![make single parameter a v3](images/appendThreeParams.png)

![](../images/line2.png)

##### `Step 9.`\|`UE5MAT`| :small_orange_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Now we need to create a material to hold this material function.  Create a new **Material** in the **Materials | Master** folder and call it `M_WorldAligned_MSRAO`. Open it up and drag **MF_WorldAlignedOpaque** to the graph. 

![make single parameter a v3](images/makeMaster.png)

![](../images/line2.png)

##### `Step 10.`\|`UE5MAT`| :large_blue_diamond:

Now connect all the pins, and they should be self evident if you named them properly.

![make single parameter a v3](images/matchPins.png)

![](../images/line2.png)

##### `Step 11.`\|`UE5MAT`| :large_blue_diamond: :small_blue_diamond: 

Lets create a material instance for these textures.  Lets start with a normal brick wall in local space (as we have done prior). 

Go to **Materials | Master** and right click on **M_Opaque_MSRAO** and select **Create Material Instance**.  Call it `MI_BrickWall`.  Move it to the **Material | MaterialInstances** folder.

![create mi_brickwall material instance](images/materialInstance.png)

![](../images/line2.png)


##### `Step 12.`\|`UE5MAT`| :large_blue_diamond: :small_blue_diamond: :small_blue_diamond: 

Scoot over to **Room 9** and and drag 5 instances of **Meshes | Supplied | SM_Wall** to the level.  Change the **Transoform | Scale** to `0.5` on all axis. Make sure there is no z-fighting.  And place two next to each other.

![alt_text](images/worldAlignedM.png)

![](../images/line2.png)

##### `Step 13.`\|`UE5MAT`| :large_blue_diamond: :small_blue_diamond: :small_blue_diamond:  :small_blue_diamond: 

Open up **MI_BrickWall** and assign **T_BrickWall_BCH**, **T_BrickWall_N** and **T_BrickWall_MSRAO** to the appropriate texture. You should see a nice brick wall.

![alt_text](images/SetTexturesNormalBrick.png)

![](../images/line2.png)

##### `Step 14.`\|`UE5MAT`| :large_blue_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:  :small_blue_diamond: 

Put **MI_BrickWall** on the wall pieces.  Again, make sure there are no rendering issues and make relevant adjustements.

Notice close in that the brick pieces don't line up.  We would have to manually adjust the position of the brick in each material to line them up perfectly and this would take a lot of time!

![close up of bricks](images/tilesDontAlign.png)

![](../images/line2.png)

##### `Step 15.`\|`UE5MAT`| :large_blue_diamond: :small_orange_diamond: 

Now lets create a world aligned material to see the difference.  Go to **Materials | Master** and right click on **M_WorldAligned_MSRAO** and select **Create Material Instance**.  Call it `MI_BrickWorldSpace` and move it to the **Materials | Material Instances** folder.

![close up of bricks](images/waMi.png)

![](../images/line2.png)

##### `Step 16.`\|`UE5MAT`| :large_blue_diamond: :small_orange_diamond:   :small_blue_diamond: 

Open up **MI_BrickWorldSpace** and assign **T_BrickWall_BCH**, **T_BrickWall_N** and **T_BrickWall_MSRAO** to the appropriate texture. Change the **Texture Size** to `64`.

![close up of bricks](images/assignWorldAlignment.png)

![](../images/line2.png)

##### `Step 17.`\|`UE5MAT`| :large_blue_diamond: :small_orange_diamond: :small_blue_diamond: :small_blue_diamond:

Duplicate the wall pieces and assign the new **MI_BrickWorldSpace** to them. 

![output to texture size](images/supeWSMat.png)

![](../images/line2.png)

##### `Step 18.`\|`UE5MAT`| :large_blue_diamond: :small_orange_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

Adjust the **TextureSize** to `600` as this matches the size on the left.  Notice that the seam between these two walls lines up perfectly.  The video below will show this off more effectively.

![make material instance for world bricks](images/matInGame.png)

![](../images/line2.png)

##### `Step 19.`\|`UE5MAT`| :large_blue_diamond: :small_orange_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond: :small_blue_diamond:

*Press* the <kbd>Play</kbd> button and look at the textures.  The ones on the right are all aligned.  Stop the game and move the wall pieces on the left.  The material moves with the object.  If you move the wall pieces on the right you texture stays fixed to world space and the object moves (almost like it is a window).  This is an effective way to get geometric materials to align between surfaces with no tweaking.  Organize your **Outliner** as this will be it for this walk through!

https://github.com/LSU-UE5/UE5-Materials/assets/5504953/138ecbdb-54a1-4399-959b-1d861826f21a

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

| `materials.ue5`\|`THE END`| 
| :--- |
| **That's All Folks!** Thanks for sticking around. That's it for this lesson. |

![](../images/line.png)

<!-- <img src="https://via.placeholder.com/1000x100/45D7CA/000000/?text=Thats All Folks!"> -->
![next up next tile](images/banner.png)

![](../images/line.png)

| [previous](../export-textures/README.md#user-content-export-textures)| [home](../README.md#user-content-ue5-intro-to-materials)
|---|---|
