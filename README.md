Download Link: https://assignmentchef.com/product/solved-computer-vision-lab-exercise-7
<br>
<h1> Surface rendering</h1>

In previous lab exercises, you have worked on estimating the 3D shape of an object from images. Once the shape is recovered in terms of a point cloud, the original texture of the object in image can be mapped to the point cloud to improve the visualization.

In this lab, you will create a function that takes as input the reconstructed point cloud (The merged cloud after aligning with procrustes), the entries of the motion matrix corresponding to the first point (so first two rows). The mean of the measurement matrix across rows and the image you consider as the main view.

<strong>Note : </strong>This lab will require some modification of the previously created <em>reconstruction demo.m </em>file.

Please modify the script as suggested below. This modification is necessary because we require the variables ‘M1’ and ‘MeanFrame1’ for the surface rendering function.

After this line (calling sfm on the measurement matrix),

<table width="527">

 <tbody>

  <tr>

   <td width="27">1</td>

   <td width="500">[M, S, p]=sfm(X);</td>

  </tr>

 </tbody>

</table>

Add the following lines:

<table width="527">

 <tbody>

  <tr>

   <td width="27">1</td>

   <td width="500">% Save the M matrix and Meanvalues for the first frame. In …this example,the</td>

  </tr>

  <tr>

   <td colspan="2" width="527">2                      % first frame is the camera plane (view) where every point …will be projected3                      % Please do check if M is non-zero before selection. … Otherwise, you4                      % have to select another view5                      if i==1 &amp;&amp; ¬p6                      M1=M(1:2,:);7                      MeanFrame1=sum(X,2)/numPoints;8                      end</td>

  </tr>

 </tbody>

</table>

<h2>1      Surface rendering</h2>

Once the necessary changes have been made to the <em>reconstruction demo.m </em>file, proceed to fill out the surface render function. The steps are detailed below.

<ol>

 <li>Assign columns of the computed point cloud to their respective coordinate axes (They should be in the order X, Y, Z)</li>

</ol>

<table width="488">

 <tbody>

  <tr>

   <td width="59">1</td>

   <td width="429">% (X,Y,Z) of the point cloud</td>

  </tr>

  <tr>

   <td width="59">2</td>

   <td width="429">pointcloud = unique(pointcloud’, ‘rows’)’;</td>

  </tr>

  <tr>

   <td width="59">3</td>

   <td width="429">X = …</td>

  </tr>

  <tr>

   <td width="59">4</td>

   <td width="429">Y = …</td>

  </tr>

  <tr>

   <td width="59">5</td>

   <td width="429">Z = …</td>

  </tr>

 </tbody>

</table>

The MATLAB fucntion surf can be used to display the texture surface. But, surf uses all points given as input. So, points that are occluded in the images would then need to be removed. To achieve this, you can compute the viewing direction, place a clipping plane at the centre of mass of the object and remove points that lie behind the plane.

<ol start="2">

 <li>Compute the cross product of the X and Y entries of M and normalize:</li>

</ol>

<table width="488">

 <tbody>

  <tr>

   <td width="59">1</td>

   <td width="429">viewdir = cross(M(1,:), M(2,:));</td>

  </tr>

  <tr>

   <td width="59">2</td>

   <td width="429">viewdir = viewdir/sum(viewdir);</td>

  </tr>

 </tbody>

</table>

<ol start="3">

 <li>Determine the means along each axis of the point cloud(X, Y and Z). ‘X0’ contains as elements the transposed columns of the point clouds you assigned in step 1. Tile ‘X1’ and ‘Xm’ to be the same size as X1 with the computed viewing direction and mean respectively.</li>

</ol>

<table width="488">

 <tbody>

  <tr>

   <td width="59">1</td>

   <td width="191">m = [… ;… ;… ];</td>

   <td width="237"></td>

  </tr>

  <tr>

   <td width="59">2</td>

   <td width="191">X0 = [… ;… ;… ];</td>

   <td width="237"></td>

  </tr>

  <tr>

   <td width="59">3</td>

   <td width="191">X1 = repmat(viewdir,…</td>

   <td width="237">,… );</td>

  </tr>

  <tr>

   <td width="59">4</td>

   <td width="191">Xm = repmat(m,… ,…</td>

   <td width="237">);</td>

  </tr>

 </tbody>

</table>

<ol start="4">

 <li>Find the indices where the dot product between the mean subtracted points (given by ‘X0 – Xm’) and the viewing direction is negative. These are the points that need to be removed.</li>

</ol>

<table width="488">

 <tbody>

  <tr>

   <td width="59">1</td>

   <td width="429">indices = find(…);</td>

  </tr>

  <tr>

   <td width="59">2</td>

   <td width="429">X(indices) = [];</td>

  </tr>

  <tr>

   <td width="59">3</td>

   <td width="429">Y(indices) = [];</td>

  </tr>

  <tr>

   <td width="59">4</td>

   <td width="429">Z(indices) = [];</td>

  </tr>

 </tbody>

</table>

<ol start="5">

 <li>Define the grid that your surface can then be created on. The array ‘ti’ defines the range of values (for eg., -500:500 or -700:2.5:700)</li>

</ol>

<table width="488">

 <tbody>

  <tr>

   <td width="59">1</td>

   <td width="429">ti = …</td>

  </tr>

  <tr>

   <td width="59">2</td>

   <td width="429">[qx,qy] = meshgrid(… , …);</td>

  </tr>

 </tbody>

</table>

<ol start="6">

 <li>Use the in-built MATLAB function TriScatteredInterp to determine the interpolant and apply it to the defined grid.</li>

</ol>

<table width="488">

 <tbody>

  <tr>

   <td width="59">1</td>

   <td width="429">% Surface generation using TriScatteredInterp</td>

  </tr>

  <tr>

   <td width="59">2</td>

   <td width="429">% You can also use scatteredInterpolant instead.</td>

  </tr>

  <tr>

   <td width="59">3</td>

   <td width="429">% Please check the detailed usage of these functions</td>

  </tr>

  <tr>

   <td width="59">4</td>

   <td width="429">F = TriScatteredInterp(…);</td>

  </tr>

  <tr>

   <td width="59">5</td>

   <td width="429">qz = F(qx,qy);</td>

  </tr>

 </tbody>

</table>

<ol start="7">

 <li>Reshape the grid values to row vectors of the number of elements.(<strong>Hint: </strong>Have a look at the numel function)</li>

</ol>

<table width="488">

 <tbody>

  <tr>

   <td width="59">1</td>

   <td width="429">% Reshape (qx,qy,qz) to row vectors for next step</td>

  </tr>

  <tr>

   <td width="59">2</td>

   <td width="429">qxrow = …</td>

  </tr>

  <tr>

   <td width="59">3</td>

   <td width="429">qyrow = …</td>

  </tr>

  <tr>

   <td width="59">4</td>

   <td width="429">qzrow = …</td>

  </tr>

 </tbody>

</table>

<ol start="8">

 <li>Transform the reshaped grid (with the reshaped rows each occupying a different row) to the reference view by multiplying with the motion/transformation matrix: <em>M</em>. To compensate for the mean subtraction step with the measurement matrix – means of the frame (the ‘Meanframe’ variable from the <em>reconstruction demo.m</em>) along each axis need to be added to the corresponding grid axis.</li>

</ol>

<table width="488">

 <tbody>

  <tr>

   <td width="59">1</td>

   <td width="429">% Transform to the main view using the …corresponding transformation matrix</td>

  </tr>

  <tr>

   <td width="59">2</td>

   <td width="429">q xy = …</td>

  </tr>

  <tr>

   <td width="59">34</td>

   <td width="429">% All transformed points are normalized by mean …values in advance, we have to move</td>

  </tr>

  <tr>

   <td width="59">5</td>

   <td width="429">% them to the correct positions by adding …corresponding mean values of each dimension.</td>

  </tr>

  <tr>

   <td width="59">6</td>

   <td width="429">q x = …</td>

  </tr>

  <tr>

   <td width="59">7</td>

   <td width="429">q y = …</td>

  </tr>

 </tbody>

</table>

<ol start="9">

 <li>Assign image channels (R,G,B) to the appropriate variable(‘imgr’, ‘imgg’, ‘imgb’). For each colour channel, obtain the intensity values at linear indices given by ‘y ’(row subscript) and ‘x’(column subscript) grid values.</li>

</ol>

[<strong>Hint</strong>: look at the sub2ind function and also note that the grid values need to be rounded to serve as subscripts]

<table width="488">

 <tbody>

  <tr>

   <td width="59">1</td>

   <td width="429">if(size(img,3)==3)</td>

  </tr>

  <tr>

   <td width="59">2</td>

   <td width="429">% Select the corresponding r,g,b image channels</td>

  </tr>

  <tr>

   <td width="59">3</td>

   <td width="429">imgr = …</td>

  </tr>

  <tr>

   <td width="59">4</td>

   <td width="429">imgg = …</td>

  </tr>

  <tr>

   <td width="59">5</td>

   <td width="429">imgb = …</td>

  </tr>

  <tr>

   <td width="59">67</td>

   <td width="429">% Color selection from image according to …(q y, q x) using sub2ind</td>

  </tr>

  <tr>

   <td width="59">8</td>

   <td width="429">Cr = …</td>

  </tr>

  <tr>

   <td width="59">9</td>

   <td width="429">Cg = …</td>

  </tr>

  <tr>

   <td width="59">10</td>

   <td width="429">Cb = …</td>

  </tr>

 </tbody>

</table>

You will then display the surface given the rendering parameters and shading.

<h2>             2      Output</h2>

Here are displayed the rendered version of the teddy bear object for reference. You can use it to compare against your own implementation.