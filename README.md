# NiagaraGPU-SkeletalMeshEcho
A GPU enabled technique for visualizing a live running history of skeletal mesh animation using Niagara, static mesh instances, simulation stages and 2D render targets in Unreal Engine 5.1.1

![](https://github.com/dbsierra/NiagaraGPU-SkeletalMeshEcho/blob/main/Media/gif1.gif)

Model and animation from Mixamo

**How it works:**

Essentially we are generating our own VAT (Vertex Animation Texture) on the fly every frame, for the positions and normals of the given skeletal mesh. On every frame we write the current vertex animation data for that frame, then we move every other frame of vertex data written previously in the texture down one tile. This keeps a running history of the last N frames of animation in our texture.

These VATs get used in a material which gets applied to static mesh instances in Niagara. Each static mesh instance uses its unique instance id to know where in the texture history it should grab its vertex data from. The gif below shows you what these textures look like while playing the animation. The top is position offset data (vertex skinned position - vertex base pose position), and the bottom is normals.

![](https://github.com/dbsierra/NiagaraGPU-SkeletalMeshEcho/blob/main/Media/VAT.gif)

While it does incur a cost to always write new data to the texture every frame, by doing it this way we can use live mocap data if we wanted, or easily change whatever animation the skeleton is doing on the fly and have our instances update automatically, since no offline texture creation is required.

If you wanted to use this in your own project, first you would need to import in a static mesh version of your skeletal mesh (no bones or skinning), and the skeletal mesh and static mesh both need to have UV information that says which pixel in the VAT to write and read data to/from. In my project I used Houdini to do this, and have included the source file as well. I happened to write this data in the 2nd UV channel, but you can write this in any UV channel as long as you read from the same channel in Unreal.

![](https://github.com/dbsierra/NiagaraGPU-SkeletalMeshEcho/blob/main/Media/Houdini.jpg)

