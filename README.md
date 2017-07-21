#### Objective:
Solar energy use has been steadily increasing in the US for the past decade at an average rate of 68% per year.  Much of this
growth is from distributed residential solar, which is most often solar panels on the roofs of houses.  This distributed solar
energy has been historically difficult to integrate into the electrical grid due to the two way electricity flow created
between times of high and low solar irradiance.  Accurate real time predictions of the net energy added to the grid could be
helpful for utility companies to manage the distributed solar energy contribution, especially in areas where solar energy can
account for a significant portion of the daytime power production (up to 6% of the energy used in CA currently comes from
distributed solar).   As the number of solar installations continues to increase they will become more difficult to track and
monitor.  With this in mind, the goal of this project is to develop a tool to locate solar panels in high resolution satellite
images. This tool could passively monitor the size and location of distributed residential solar panels and, in combination
with real time local solar irradiance and predicted solar irradiance, could be used to predict the energy output of an area of
interest and plan electrical distribution accordingly.

#### Data:
The Amazon Web Services SpaceNet dataset provided by DigitalGlobe, Inc. contains about 4500 30 cm resolution, 8-
band multispectral images with corresponding building footprints from Las Vegas, NV. From manual inspection, around 30% of
these images were found to contain solar panels, making this dataset a good candidate for model development.

#### Methods:
Segmentation and a U-Net model were used for this project.  Segmentation allows image classification pixel by pixel, which
gives a clean boundary on each object identified in the image. This allows the objects to be counted and their sizes estimated,
which is important for estimating the power output of solar panels. The U-Net model is a deep learning architecture that was
developed to detect cancer using biomedical image segmentation.

Solar panels first had to be located and their boundaries traced to use as training data. This was done manually us the QGIS
software. Then masks for both the solar panels and the building footprints were created using geopandas and shapely. Since the
actual number of solar panel pixels was found to be only ~0.2% of the total pixels in the dataset, the solar panel data was
upsampled to account for the class imbalance. This included using only images with solar panels, generating additional solar
panel images by mirroring and rotating the images, and increasing the size of the solar panel images to make the features
larger. In addition to upsampling solar panel data, a U-Net model to predict building footprints was also developed to help the
solar panel model isolate where solar panels should be.

<img src="https://github.com/TorrBorr/My_Projects/blob/master/Project_5/PanelandBuildingMasks.png" width="600">

#### Results:
The building footprint model was trained to predict building footprints with ~85% precision, meaning that 85% of the pixels it
predicted were part of a building were indeed part of a building.  The predictions were added as a 9th channel to the images
that were fed to the solar panel model.  After training, the solar panel model was able to find solar panels with 35%
precision. Nearly all solar panels were identified in all test images; however, the model also predicted shadows and treetops
as solar panels which led to the decreased precision.

##### Building Footprint Model:
<img src="https://github.com/TorrBorr/My_Projects/blob/master/Project_5/BuildingFootprintModel.png" width="600">

##### Solar Panel Model:
<img src="https://github.com/TorrBorr/My_Projects/blob/master/Project_5/SolarPanelPrediction.png" width="600">

The model predictions were used to determine the number of buildings and solar panels in each image and to calculate their
area. Building prediction numbers and areas were very consistent with the true values. The solar panel predictions were much
higher in number and area than the true values due to the misidentification of shadows and trees as solar panels.

#### Conclusions and future work:
These models were able to successfully locate building footprints and solar panels and the model outputs were used to calculate
object number and size, which is a first step in developing a tool to estimate the power output of solar panels in a given
area. The solar panel locator model is currently sensitive to shadows and trees as well as solar panels, so it needs additional
tuning work. Increasing the training dataset size to give the model more examples and improving the building footprint
prediction to exclude trees next to houses should help with this.
