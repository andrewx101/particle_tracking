## Particle tracking
### Background
This app is a GUI version for the original MATLAB code written by Daniel Blair & Eric Dufresne.[^1] The app is designed for probe microrheology practice.[^2] 

Features:
* Loads and displays time-lapse images
* Allows adjustment of intensity level range for viewing the image
* Locates and tracks particles by a set of parameters
* Outputs results and metadata.

### Instalation
This app requires [the BIO-FORMATS toolbox by OME](https://www.openmicroscopy.org/bio-formats/). Please make sure this toolbox is properly installed in your MATLAB environment.

Unzip and drag the `.mlappinstall` file to the command window of MATLAB. After the installation it will appear in the app list. 

### Load time-lapse files
The supported formats of raw images are indicated in the "File type" panel. 

The ND2 format is generated from a Nikon microscope. Time-lapse images should be contained in a single ND2 file.

The FITS format is exported by the Solis software of Andor detector, or by the  pco.camware software of pco detector. For FITS format, a pixel depth of 16-bit (int16) is assumed. The Solis software will split the exported FITS files into portions. So please select all FITS files of the same time-lapse series in the file loading window in this case.

Support for more widely used formats (e.g. TIFF stacks) is not written at this moment.

Click the "Load..." button. If the file are successfully loaded, the table below will display the meta info of this time-lapse series. Please notice any error message display in the "Messages" text box.

Also, if the loading process is successful, the first frame will show on the right, and all sliders in the GUI will be updated according to the actual information of the loaded files.

### Viewing the images
If ever a time-lapse series is successfully opened, you can drag the "Frame" slider or change the spinner box beside it to select which frame to show in the figure.

Adjust the "Level range" slider to vary the range of intensity level by which the current frame is displayed. Note that the app assumed the particles are brighter spots than the background, not the inverse case.

Also use the tools on the figure control for spanning, zooming, data picking, etc.

### Locating particles
Now we need the computer to identify the locations of the particles in each frame. This is done under the "Locating & tracking options" panel.

Enter the diameter (in pixels) of a typical particle spot in the raw image in the textbox next to the "Band-pass filter" checkbox. Check this checkbox to preview the result of band-pass filtering on the current frame. More details about this step can be found elsewhere.[^1][^3] In these sources, please find information related to "bpass" for an understanding of the diameter parameter.

Enter the diameter (in pixels) within which the computer could determine the maximum intensity as a raw location of a particle, in the text box next to the "Find peaks" checkbox. And also determine the minimum brightness (intensity value) below which the computer should not consider as a particle, by adjusting the spinner or slider next to the label "Min brightness". If this value is set too low, some artifact spots will be considered particles and be located. Please browse through more frames by the "Frame" slider or spinner to check if this happens to some frames. More details about this step can be found elsewhere.[^1][^3] In these sources, please find information related to "pkfnd"for an understanding of the diameter parameter and the minimum brightness parameter.

The peak finding process provides two set of results. The first set is the raw locations of the particles. These are displayed on the image as "+" symbols. The second set is the refined locations by Gaussian profile fitting. These are displayed on the image as "x" symbols. This step is done automatically in the current app. In other sources,[^1][^3] the refining step corresponds to the contents about the "cntrd" function. Check the "Find peaks" checkbox to preview the results over the image.

Note that if the "Band-pass filter" checkbox is unchecked, the peak finding is based on the raw images, rather than the band-passed images.

If you are contend with the parameters, click "Locate all frames" button to process all frames with the current set of parameters. This will take a long time, and the "Messages" textbox will show the progress.

Note that the plotted results when we change the parameters are only for previewing. Only after clicking the "Locate all frames" button do we have the position list ready for tracking.

### Tracking
In this step the computer can link the locations of particle in each frame into trajectories. To do this properly the computer needs to know three parameters.

* "Max. Disp." textbox: The maximum displacement possible for a particle. Effectively, this parameter tells the computer not to link two positions larger than this range.
* "Memory" textbox: If two locations satisfies the condition of linking, but they are separated by a number of frames (e.g. a particle moves out of focus in a while and returns back in focus), the computer need this criterion (number of lag frames) to determining whether to link them into the same trajectory or to split them into two trajectories.
*  "Min. length" textbox: After the tracking process, some trajectories will be very short which might not be of great value. The computer discards trajectories with number of steps less then this parameter.

Please find more details about these parameter in other sources [^1][^3].

Click "Track" button to start the tracking process. Usually this only takes several seconds. The logs will show in the "Messages" textbox. Please beware of the last line of message which will indicate failures of tracking. 

If the tracking process is success, all tracks will be plotted over the image on the right, with the position of the current frame indicated as a "x" symbol.

Note that if you have clicked "Locate all frames" button when "Band-pass filter" checkbox was unchecked, the result of tracking is based on the raw images anyways, even if you check the "Band-pass filter" checkbox again and the band-passed image is displayed under the results of tracks.

You may want to zoom the image to view the result of a particular particle. It is a fun to change the current frame by the spinner and see how the position of this particle shift along the trajectory.

Click “Clear tracks” to clear the tracks data and the plotted trajectories in the figure.

### Save outputs into files
Click "Save data" button and a number of files will be saved under the same path of the opened files.

Three sets of data will be saved to  `PTOutput.mat` file if they are not empty. This file will be saved at the same folder of the imported time-lapse series.

1. The meta data. These are the content in the table control. It will be save in `meta` (table) in the output `.mat` file. In addition, the shutter time is saved in `dt` (double), and the calibrated pixel size will be save in `PixelSize` (double) . 
2. The peak finding data. These includes the following variables

​	`blnBPass` (boolean): whether the peak finding was based on band-pass filtered images

​	`BPassDia` (double): the diameter for band-pass filtering

​	`PkfndDia` (double): the diameter for peak finding

​	`MinBrightness` (double): the cut-off brightness for peak finding

​	`br` (double): average brightness of the particle in the raw images

​	`meanNoise` (double): mean noise level of the raw images

​	`stdNoise` (double): standard deviation of the noise level of the raw images

​	`snr` (double): the signal-to-noise ratio of the raw images

​	`poslst` (4-column double): the position list data

Details about the matrix `poslst` can be found elsewhere.[^1][^3] In reference [^1], find information about "pos_lst". In reference [^3], find information about "poslist".

3. The tracks data. These includes the following variables

​	`MaxDisp` (double): the maximum displacement used for the tracking process

​	`Memory` (double): the memory parameter used for the tracking process

​	`MinLength` (double): the minimum length parameter used for the tracking process

​	`tr` (4-column double): the tracking data See the README of [Track Analysis](https://github.com/andrewx101/track_analysis/) app for details.

If any of the three set of data are empty the app will notice you in the “Message” textbox. In any case, the full path of the output file will be given in the “Message” textbox

### Footnotes
[^1]: Please refer to [this website](https://site.physics.georgetown.edu/matlab/) for more details.
[^2]: This `readme.md` and also the app are for users familiar with the field of probe microrheology. See for example: Waigh, T. A. "Microrheology of Complex Fluids." *Reports on Progress in Physics* 68, no. 3 (2005): 685-742.
[^3]: [This handout](https://lem.che.udel.edu/wiki/uploads/Main/Microrheology/Particle_tracking_handout.pdf) is also a source of details for the algorithms used by this app.
