---
title: "Readme"
author: "André Chagas"
date: "23 de agosto de 2015"
output: html_document
---
#Getting and Cleaning Data Course Project
##Purpose

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit:
  
* A tidy data set as described below;
* A link to a Github repository with your script for performing the analysis; 
* A code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md.

You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.

##Objectives

run_analysis.R performs the following:
  
1. Merges the training and the test sets to create one data set.
2. Extracts only the measurements on the mean and standard deviation for each measurement.
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive activity names.
5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject.

##Get the data

1. Download the file and put the file in the <strong>data</strong> folder.

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
if(!file.exists("./data")){dir.create("./data")}
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./data/Dataset.zip",method="curl")
</code>
</pre>

2. Unzip the file.

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
unzip(zipfile="./data/Dataset.zip",exdir="./data")
</code>
</pre>

3. Unzipped files are in the folderUCI HAR Dataset. Get the list of the files.

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
path_rf <- file.path("./data" , "UCI HAR Dataset")
files<-list.files(path_rf, recursive=TRUE)
files
</code>
</pre>

##Read data from the targeted files

1. Read data from the files into the variables

Read the Activity files

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
dataActivityTest  <- read.table(file.path(path_rf, "test" , "Y_test.txt" ),header = FALSE)
dataActivityTrain <- read.table(file.path(path_rf, "train", "Y_train.txt"),header = FALSE)
</code>
</pre>

Read the Subject files

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
dataSubjectTrain <- read.table(file.path(path_rf, "train", "subject_train.txt"),header = FALSE)
dataSubjectTest  <- read.table(file.path(path_rf, "test" , "subject_test.txt"),header = FALSE)
</code>
</pre>

Read Fearures files

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
dataFeaturesTest  <- read.table(file.path(path_rf, "test" , "X_test.txt" ),header = FALSE)
dataFeaturesTrain <- read.table(file.path(path_rf, "train", "X_train.txt"),header = FALSE)
</code>
</pre>

2. Look at the properties of the above varibles

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
str(dataActivityTest)
</code>
</pre>
<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
str(dataActivityTrain)
</code>
</pre>
<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
str(dataSubjectTrain)
</code>
</pre>
<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
str(dataSubjectTest)
</code>
</pre>
<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
str(dataFeaturesTest)
</code>
</pre>
<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
str(dataFeaturesTrain)
</code>
</pre>

##Merges the training and the test sets to create one data set

1. Concatenate the data tables by rows.

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
dataSubject <- rbind(dataSubjectTrain, dataSubjectTest)
dataActivity<- rbind(dataActivityTrain, dataActivityTest)
dataFeatures<- rbind(dataFeaturesTrain, dataFeaturesTest)
</code>
</pre>

2. Set names to variables.

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
names(dataSubject)<-c("subject")
names(dataActivity)<- c("activity")
dataFeaturesNames <- read.table(file.path(path_rf, "features.txt"),head=FALSE)
names(dataFeatures)<- dataFeaturesNames$V2
</code>
</pre>

3. Merge columns to get the data frame Data for all data

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
dataCombine <- cbind(dataSubject, dataActivity)
Data <- cbind(dataFeatures, dataCombine)
</code>
</pre>

##Extracts only the measurements on the mean and standard deviation for each measurement

1. Subset Name of Features by measurements on the mean and standard deviation i.e taken Names of Features with "mean()" or "std()"

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
subdataFeaturesNames<-dataFeaturesNames$V2[grep("mean\\(\\)|std\\(\\)", dataFeaturesNames$V2)]
</code>
</pre>

2. Subset the data frame Data by seleted names of Features

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
selectedNames<-c(as.character(subdataFeaturesNames), "subject", "activity" )
Data<-subset(Data,select=selectedNames)
</code>
</pre>

3. Check the structures of the data frame Data

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
str(Data)
</code>
</pre>

##Uses descriptive activity names to name the activities in the data set
1. Read descriptive activity names from "activity_labels.txt".

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
activityLabels <- read.table(file.path(path_rf, "activity_labels.txt"),header = FALSE)
</code>
</pre>

2. facorize Variale activity in the data frame Data using descriptive activity names

3. Check.

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
head(Data$activity,30)
</code>
</pre>

##Appropriately labels the data set with descriptive variable names

In the former part, variables activity and subject and names of the activities have been labelled using descriptive names.In this part, Names of Feteatures will labelled using descriptive variable names.

* prefix t is replaced by time
* Acc is replaced by Accelerometer
* Gyro is replaced by Gyroscope
* prefix f is replaced by frequency
* Mag is replaced by Magnitude
* BodyBody is replaced by Body

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
names(Data)<-gsub("^t", "time", names(Data))
names(Data)<-gsub("^f", "frequency", names(Data))
names(Data)<-gsub("Acc", "Accelerometer", names(Data))
names(Data)<-gsub("Gyro", "Gyroscope", names(Data))
names(Data)<-gsub("Mag", "Magnitude", names(Data))
names(Data)<-gsub("BodyBody", "Body", names(Data))
</code>
</pre>

Check

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
names(Data)
</code>
</pre>

##Creates a second,independent tidy data set and ouput it

In this part,a second, independent tidy data set will be created with the average of each variable for each activity and each subject based on the data set in step 4.

<pre id="mycode" class="haskell numberLines" startFrom="100">
<code>
library(plyr);
Data2<-aggregate(. ~subject + activity, Data, mean)
Data2<-Data2[order(Data2$subject,Data2$activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)
</code>
</pre>