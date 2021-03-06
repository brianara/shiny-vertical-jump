# Shiny Vertical Jump Analysis
An open-source solution for vertical jump analysis

# Table of contents
* [What is this?](#what-is-this)
* [Downloading R / Installing Required Packages](#downloading-r)
* [Running the app](#running-the-app)
* [Preparing your data](#preparing-your-data)
* [Analyzing your data](#analyzing-your-data)
* [Wrapping up](#wrapping-up)

## What is this?
This Shiny app is geared toward strength and conditioning professionals and researchers who can't afford vertical jump analysis software and those who don't have the coding knowledge to design their own analyses. It's my hope these individuals will have an alternative to analyzing their force-time data in Excel (we've all been there). This allows for more powerful analysis than just simply jump height, peak force, etc. and allows for much faster processing of large amounts of data. Additionally, this app is publicly available to take the cover off the black box that is force-time data analysis. A freely visible set of filtering, plotting, and variable calculation functions allow for consistent analysis procedures between users. 

As of 2019-11-06, the app now functions with a variety of data layouts (Pasco, wide, long, and single trial) and with both .csv and .txt files. This app is published under the GNU GPL-3 license; you're free to use, modify, and share the source code and your own modified code as long as you release your modification(s) under the same license. See [this site](https://resources.whitesourcesoftware.com/blog-whitesource/top-10-gpl-license-questions-answered) for a plain-English explanation of GPL-3.

## Downloading R
(I haven't tested compatibility with MacOS or Linux. Feel free to share any hiccups you encounter if you use either platform.) The first step in running an R-based program is to, well, have R installed on your computer. The most up-to-date version of R can be found [here](https://cran.r-project.org/bin/windows/base/). The app was built with R version 3.6.1 and packages that were current as of 2019-11-06. I can't promise everything will work correctly post-installation, as package updates can randomly break parts of the code. If you ever run into any errors, please [email me](mailto:samsperformancetraining@gmail.com) and I will look into the problem and release a hotfix ASAP. Once you have R installed, you also need [RStudio](https://www.rstudio.com/products/rstudio/download/#download). You interact with base R via an ugly, bare-bones IDE, whereas RStudio has all sorts of bells and whistles to make your experience more tolerable.

Aside from R and RStudio, you will need to install a number of support packages before the app will function. Packages expand the functionality of R past the `base` functions and can be written to solve virtually any issue. Most dependencies for this app are used to manipulate and visualize the data. Packages are installed via the ```install.packages()``` function. E.g. ```install.packages("data.table")```. Make sure to enclose the package names in '' or "", although newer versions of RStudio may automatically prompt you to install missing packages when you open the global.R file. The currently required packages are the following: `shiny`, `shinydashboard`, `data.table`, `zoo`, `signal`, `changepoint`, `purrr`, `pracma`, `plotly`, `knitr`, `kableExtra`, and `shinyjs`. Installing these packages should install any other requisite packages, but if the app refuses to load or crashes on certain functions, examine the error message before emailing me. If a package is missing, the error will tell you.

## Running the app
Once you have R and all the required packages, you can download the app as a .zip folder (click the green 'Clone or download' button on GitHub and select 'Download ZIP') or manually copy the raw code (shiny-vertical-jump > Fully Automated > global.R / app.R > Raw) to global.R and app.R files. There are both "online" and "offline" versions of the app; online if you plan on running the app on a web server (e.g. shinyapps.io), offline if you plan on performing analyses on your computer. Either will work for offline analysis, but only the online version will work on a site like shinyapps.io (it has a file download handler not present in the offline version).

If you download the app as a .zip, you'll need to extract the files. Importantly, wherever the global.R and app.R files are stored will determine where the analysis files are created. So regardless of whether you download the .zip file or manually copy the code, be sure to save the files to the correct directory. For instance, if you work with several teams and want to keep their data separate you might save copies to folders for each team. 

If you decide to have multiple copies of the app, be sure to load the correct one by selecting the correct app.R in the file viewer pane in the bottom right-hand corner of your R Studio environment.

![App location](/Tutorial/app_location.png)

I normally get around this by having a different project for each team. You can create new projects by selecting your project's name > New Project and following the prompts to add a new project file.

![New project](/Tutorial/new_project_1.png) 

![New folder](/Tutorial/new_project_2.png) 

![Project type](/Tutorial/new_project_3.png) 

![Project name](/Tutorial/new_project_4.png) 

Again, you only have to follow these steps if you have multiple copies of the file dedicated to different teams. If you only have a single copy of the file, don't worry about creating a new directory, etc. Just open the app.R in your current R studio project / environment. 

The app can be run by either pressing ctrl + shift + enter or by pressing the 'Run App' button that appears.

## Preparing your data

Unlike previous versions of the app that only worked with Pasco Capstone data, the newest version supports multiple file formats. Supported formats now include Pasco, wide format, long format, and single trial. With the exception of Pasco export data, the other files should all follow the same format. The [example data](https://github.com/mattsams89/shiny-vertical-jump/tree/master/Example%20Data) folder contains examples for each supported layout. Your data *must* follow one of these formats because the file parser function will not know how to handle other layouts. For Pasco data, be sure your vertical force data are named some variant of "Normal" force; the parser is looking for columns that include "Normal" in their name and will remove any other columns.

## Analyzing your data

Once you have everything installed and have exported your data, all that's left is the analysis. I've attempted to make analysis as pain-free as possible as you're only required to set a few pieces of information on the left-hand side of the application. The analysis and saving the results are essentially automated after that.

Run the app by pressing ctrl + shift + enter or by pressing the 'Run App' button. 

You'll see the following in the left-hand panel:
1. A file selector  
    Currently, files up to 40 MB are supported. You can change this in the app settings by altering line 14 of the global.R file: `options(shiny.maxRequestSize = 40 * 1024 ^ 2)`.
2. A trial selector
3. Dropdowns for filter application, jump type, and jump start location
4. Inputs for the athlete's name, bar load, sampling frequency, quiet standing length (in seconds), and calibration values for FP1 and FP2.

Start by selecting your file layout in the "How are the Data Arranged?" input. The app *will* crash if your selection doesn't match what you upload. After selecting the file layout, use the upload button to upload your data. It will take it a moment to upload and process depending on the file size and number of trials.

![Upload](/Tutorial/file_upload.gif)

The app is smart enough to (hopefully) auto-recognize the jump type and find quiet standing. The former is accomplished by examining the difference between body weight during quiet standing and the minimum force prior to peak force, whereas the latter is accomplished by finding the area with lowest variance in the data prior to jump initiation. If the app incorrectly guesses the jump type, you can manually set the jump type with the "Select Jump Type" dropdown. Likewise, if you don't like what the app selected for quiet standing, you can manually "brush" the top plot by clicking and dragging across the data.

![](/Tutorial/brushed_trial.png)

The height of your brush doesn't matter; only the X-axis coordinates of the brush are used to update the trial information. When brushing the plot, make sure to grab from quiet standing to any point after peak landing force. There's a good chance the app will crash otherwise. In trials where the athlete was squirrelly prior to takeoff, grabbing a full second for quiet standing may not be feasible. In such cases, you can adjust the quiet standing length with the "Quiet Standing" input on the left. The default is 1 second, but it accepts values from 0.2 - 1 in 0.1 second increments.

Once your trial is selected correctly, a quick glance at the bottom plot and plots on the right will tell you whether the app recognized the jump type correctly. I rather arbitrarily chose a 250N difference between quiet standing and minimum force prior to peak force as the delineation between SJ and CMJ. That worked well with athletes ranging from 65kg - 110kg, but it may not work as well for smaller athletes. For SJ, the app will warn you if the athlete produced a countermovement > 5SD their quiet standing weight, but it won't prevent you from saving the trial. Once you're happy with the app's selection, press save and move on to the next trial via the "Select Trial" input. The offline version will create a folder called "Analyses" and will save trials to a date stamped .csv, whereas you'll need to use the download button on the online version once you've analyzed your data.

Some quick insight into how the analysis works: the ```findpeaks``` function from `pracma` determines approximate locations for peak force and peak landing force. These points are confirmed by checking the number of points < 10N between pairs of identified peaks (202 - 903ms, or ~ 5 - 100cm, represent the cutoffs). Based on the literature, initial flight time and landing are found by determining the first and last points < 10N between these two peaks. The middle 50% of the data between initial takeoff and landing are used to calculate the mean + 5*SD of the unloaded plates' force values, and true takeoff and landing are the first and last points above this threshold value. ```cpt.var``` is used to find a 1 second interval with the lowest variance prior to takeoff; this is the app's best guess for the quiet standing phase. Like I mentioned above, jump type is guessed by examining the difference between quiet standing mean force and minimum force prior to takeoff. The app employs different rules for SJ and CMJ. For SJ, quiet standing mean force + 5x the SD of quiet standing is used as the jump initiation threshold, whereas the opposite is used for CMJ. Finally, depending on your selection in the "Jump Start Location" input, the app either searches backward from this threshold to find the first point coinciding to body weight identified during quiet standing OR steps backward 30ms from the threshold. This point represents the true jump start. There is some additional pre-movement checking that occurs to handle pre-jump countermovements (for SJ) or pre-countermovement increases in force (for CMJ). You can read more about the math behind these methods in the app.R file's comments.

Once jump start is identified, lots of math happens. What you probably need to know: impulse is determined via the trapezoidal rule (`cumtrapz` from ```pracma```), average RFD is onset to peak force, FP1-FP2 asymmetry are represented by symmetry index values, and the method for determining unweighting, braking, and propulsion is explained in Dr. Satoshi Mizuguchi's [dissertation](https://dc.etsu.edu/etd/1459/).

Once you're done analyzing data, you can stop the application by pressing the stop sign in Rstudio.

## Wrapping up

And there you have it. If you've followed along, you should now have some analyzed vertical jump data. If you received errors or ran into other problems, please don't hesitate to contact me: <SamsPerformanceTraining@gmail.com> or [@DrMattSams](https://twitter.com/DrMattSams).