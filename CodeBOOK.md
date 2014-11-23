#CodeBook


##Original Data

This dataset is a tiny dataset resulted from a transformation of a bigger set of data, namely

    Davide Anguita, Alessandro Ghio, Luca Oneto, Xavier Parra and Jorge L. Reyes-Ortiz. Human Activity Recognition on Smartphones using a Multiclass Hardware-Friendly Support Vector Machine. International Workshop of Ambient Assisted Living (IWAAL 2012). Vitoria-Gasteiz, Spain. Dec 2012

The original data source can be found at http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Original dataset description taken from its website:

    The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data. 
    
    The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). The sensor acceleration signal, which has gravitational and body motion components, was separated using a Butterworth low-pass filter into body acceleration and gravity. The gravitational force is assumed to have only low frequency components, therefore a filter with 0.3 Hz cutoff frequency was used. From each window, a vector of features was obtained by calculating variables from the time and frequency domain. 
    
    For each record in the dataset it is provided: 
    - Triaxial acceleration from the accelerometer (total acceleration) and the estimated body acceleration. 
    - Triaxial Angular velocity from the gyroscope. 
    - A 561-feature vector with time and frequency domain variables. 
    - Its activity label. 
    - An identifier of the subject who carried out the experiment.


##Data Acquisition

The data is acquired automatically by the script. Please make sure to set your working directory at line 6 of the script, variable ```working.directory```.

When the script is run, the following steps happen for obtaining the data:

 1. A directory named data is created (if not already existing) in the current working directory
 2. The working directory is switched to the data folder
 3. If the original data set (named *dataset.zip*) does not exist already, the data set is downloaded from https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip and named dataset.zip
 4. The dataset is unzipped. The original dataset is present in data/UCI HAR

##Data Transformation

Starting from the originary dataset, the following high-level steps are performed

 1. The files features.txt, *train/X_train.txt*, and *test/X_test.txt* are loaded as data tables. The X_ tables are combined via ```rbind()```. At this point, the initial dataframe *features* is comprised of 561 variables, as follows.

 |features   |
 |:----------|
 |X_train    |
 |X_test     |
 | VARS:561  |
 | OBSS:10299|
 2. As requested by step 2 of the [Project page](https://class.coursera.org/getdata-003/human_grading/view/courses/972136/assessments/3/submissions), only the measurements of the mean and the standard deviation are retained from the originary dataset. This is achieved via ```df <- df[, grep(".*\\.(mean|std)\\.\\..*", names(df), value=T)]``` The intermediary dataframe *features* is comprised of 66 variables at this point

 |features   |
 |:----------|
 |X_train    |
 |X_test     |
 | VARS:66   |
 | OBSS:10299| 
 3. The subject files *train\subject_train.tx*t and *test\subject_test.txt* are loaded and combined via the rbind() command

 |subjects       |
 |:--------------|
 |Subject_train  |
 |Subject_test   |
 | VARS: 1       |
 | OBSS:10299    |
 4. The activity files *train\y_train.txt* and *test\y_test.txt* are loaded and combined via the rbind() command in the *activities* dataframe

 | activities    |
 |:--------------|
 |y_train        |
 |y_test         |
 | VARS: 1       |
 | OBSS:10299    |
 5. As requested by step 3 of the Project page, the descriptive names for the activities, found in *activity_labels.txt*, are used in the intermediary *activities* dataset. The dataframes *subjects* and *activities* are cbind()'ed

 | subjects      | activities |
 |:--------------|:------------
 |Subject_train  | y_train    |
 |Subject_test   | y_test     |
 | VARS: 1       | VARS: 1    |
 | OBSS:10299    | OBSS: 10299|
 6. Point 1 of the Project page is performed. The training and the test sets are merged to create one data set. 
 
 |features   |subjects       | activities |
 |:----------|:--------------|:-----------|
 |X_train    |Subject_train  | y_train    |
 |X_test     |Subject_test   | y_test     |
 | VARS:66   | VARS: 1       | VARS: 1    |
 | OBSS:10299| OBSS:10299    | OBSS: 10299|
 At this point, the combined dataframe *df* is comprised of 68 variables.
 7. A series of regular expressions clean the name of the variables. The variables of the original data set are in the form ```fBodyAccJerk-mean()-Z```. R automatically converts the unfriendly symbols, e.g., ```fBodyAccJerk.mean...Z.``` The regular expressions transform the intermediary names in R coding standard-friendly names, e.g., ```f.body.acc.jerk.mean.z```. The variables are listed below.
 8. The intermediary dataframe is melted to obtain all the activities performed by all the users, and their average values. The melted database has 679734 observations of 4 variables. 
 9. An *O(N^3)* for loop is performed, first using the unique subject values, then the unique activity values per subject, then the variable values. The average values are computed and stored in the means temporary variable. Before the end of the outer loop cycle, the means variable is appended to the soon-to-be-born tidy dataset.
 10. The columns are re-ordered to have subject and activity as first columns
 11. The dataset *tidymeans.txt* is produced and exported in the original working directory (where the script is).
