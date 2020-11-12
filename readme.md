# **Architectural Feature Extraction from LIDAR data**

----------------------

### **1. THE PROMPT**

Develop a model that could accurately predict architectural features, which could be used in future work in analysis of architectural objects.

Brief aside: 'Architectural Analysis'

architectural analysis is understanding architecture through drawing, and by  drawing architecture we can start to pick out relationships/proportions/compositions/registrations in architectural features that in a way can generalize towards ‘ideal forms’ or at least help us understand the design conceits of the style/culture/history of the architectural object.


### **2. THE DATA**

The data used to train this ‘drawing’ model, is LIDAR sets provided by CYARK, a company that provides extremely detailed data sets of significant architecture. 

I chose a monastery in Armenia constructed in 1200 to focus on for this project. Armenian architecture during this time was extremely significant due to the excellence in stone construction. 

### **3. THE CHALLENGE**

There were two distinct challenges when trying to get this data to tell any valuable 'story'.

**A. Data is too big and ugly**

The raw format for this LIDAR data was approx 800,000,000 rows with about 8-9 features. For this particular set, only 7 columns were reporting actual data (the rest 0'd out) X,Y,Z,R,G,B, and a feature indicating what scan it was taken from. Note: in scanning a structure like a building, the ultimate file is a composite of several different scans stitched together. I believe this monastery was a composite of 82 different scans.

In order to tackle this, a wonderful python package **PDAL** was used to transform/manipulate/crop the data on the command line.  Forgive my lack of knowledge on this subject, but I believe this package enabled a sort of 'streaming' pipeline so my PC was able to manipulate a 20GB file 

*I give credit for this entire project to the folks who developed/are developing PDAL, as it not only in made it possible to actually work with the data, but also in feature extraction.*

The order of operations are outlined in the Feature Engineering notebook, but speaking generally, the workflow involved decimating (sampling based on KNN) to a file size that was openable by a CAD software package (Rhino 6 3D). I located a particular area of interest in the monastery, and determined crop boundaries and rotational transformations to get the area oriented on a x,y axis for analysis.

  
**B. A single row of data does not describe anything**

Once the data was in a manageable format, there really were only 3 feature columns, that really only described one thing. 

X,Y,Z -> Location.

Again, enter PDAL which allowed me to extract qualities of a point based on its relationship to other points. Refer to the Feature Engineering notebook for details on the specific qualities. But essentially, allowed me to extract eigenvalues which, to my novice understanding helps describe surface normals for a given point cloud, and what I'm conceptualizing as over-all size and density metrics. These features generally describe relationships to outliers, and points furthest from the feature point within a point cloud.

The target was equally extracted in this process, categorizing based on whether the point describes a line, plane, or volume. Volume indicating some sort of architectural feature. Think of the image of the cupola. 

### **4. THE MODEL**

Some initial notes on my modelling process

A.The train and test set were divided on the space, not by a random sampling.
B. Linear and planar were ultimately collapsed into a single value after the KNN Model, because after some additional EDA I determined that for the purposes of 'an architectural lidar space', 'linear' and 'planar' features pretty much described similar attributes.
C. I ultimately dropped the X,Y,Z global location features to see if the model generalized better.

KNN MODEL : did not work well, over sampled the minority class. I kept it in the presentation, primarily because of a nice visualization illustrating the train/test split in action.

Logistic Model: The Model ultimately performed very well, this was the MVP model and what I used to make decisions about the feature space, adding/subtracting features, etc.

XGBoost: This one was essentially a just 'plug and play' and actually drew the space out of the box, which I feel made it a huge successs. 


### **5. THE THEORY**

For this final part, I wanted to just take a moment to muse on the 'use case' or at least the bigger picture.

Architectural analysis has a 'spotty' history at-best, when considering the manual nature of the process and the inherent 'point of interest selection' at both the 'what-building-do-I-analyze' scale and at the 'what-area-of-a-building-do-I-focus-on' level. Buildings are obviously quite complex objects, and entire throughlines or histories are overlooked in analyzing them. This ultimately tends the discipline to a more 'qualitative' or 'narrative-driven' approach.

However, the ultimate goal is to consider new historical categories based on architectural form, construction, and technique rather than the classical (European) categories of architectural history culture, style, religion, era, etc. (for more on ‘recategorization’ refer to a global history of architecture: Mark Jarzombek MIT, 2015 https://www.youtube.com/watch?v=7bSIm02DTNw)

To take Jarzombek's manifesto practically, a new means by which we do analysis on architectural objects is necessary, and the altruism of CYARK's documentation provides the foundation by which this could be possible. One could imagine a model that is able to draw relationships between the stone construction of a monastery in Armenia, to similar techniques performed in other regions, and create a new 'global history' of architecture determined by economies, mobility provided through trade routes, etc. 