# GettingCleaningData
Start project with the following:
Gtting Directory by using below code:
getwd()
library(plyr)
library(data.table)
library(dplyr)
##To download zip file into folder tempporary folder
temp <- tempfile()
download.file("http://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip",temp)
unzip(temp, list = TRUE) 
This formular give the downloaded code
##This provides the list of variables and by deciding  on the ones for this data set:
##***********************************************************************************
YTrain <- read.table(unzip(temp, "UCI HAR Dataset/train/y_train.txt"))
XTrain <- read.table(unzip(temp, "UCI HAR Dataset/train/X_train.txt"))
SubjectTrain <- read.table(unzip(temp, "UCI HAR Dataset/train/subject_train.txt"))
 
YTest <- read.table(unzip(temp, "UCI HAR Dataset/test/y_test.txt"))
XTest <- read.table(unzip(temp, "UCI HAR Dataset/test/X_test.txt"))
SubjectTest <- read.table(unzip(temp, "UCI HAR Dataset/test/subject_test.txt"))

Features <- read.table(unzip(temp, "UCI HAR Dataset/features.txt"))
unlink(temp)
## setting column names
##***********************************
colnames(XTrain) <- t(Features[2])
colnames(XTest) <- t(Features[2])

#Merging X and Y train set
#************************************
XTrain$activities <- YTrain[, 1]
XTrain$participants <- SubjectTrain[, 1]
XTest$activities <- YTest[, 1]
XTest$participants <- SubjectTest[, 1]

## Assignment1 
##************
Master <- rbind(XTrain, XTest)

##Assginment 2
##**************
Mean <- grep("mean()", names(Master), value = FALSE, fixed = TRUE)

##Note you might need to include 555:559 since they have means and are associated with the gravity terms
Mean <- append(Mean, 471:477)

InstrumentMeanMatrix <- Master[Mean]

## For STD
STD <- grep("std()", names(Master), value = FALSE)

## Assignment 3
#*************
##Uses descriptive activity names to name the activities in the data set
## By changing class for string replacing purpose
Master$activities <- as.character(Master$activities)
Master$activities[Master$activities == 1] <- "runing"
Master$activities[Master$activities == 2] <- "runing down"
Master$activities[Master$activities == 3] <- "jumping up"
Master$activities[Master$activities == 4] <- "walking"
Master$activities <- as.factor(Master$activities)

##Assignment 4
##************
##Appropriately labels the data set with descriptive variable names.
names(Master)
##Using gsub and acronyms to replace the list(i.e Read more from R Crane library)
names(Master) <- gsub("Acc", "Accelerator", names(Master))
names(Master) <- gsub("Mag", "Magnitude", names(Master))
names(Master) <- gsub("Gyro", "Gyroscope", names(Master))
names(Master) <- gsub("^t", "time", names(Master))
names(Master) <- gsub("^f", "frequency", names(Master))
## changing names
Master$participants <- as.character(Master$participants)
Master$participants[Master$participants == 1] <- "Participant 1"
Master$participants[Master$participants == 2] <- "Participant 2"
Master$participants[Master$participants == 3] <- "Participant 3"
Master$participants[Master$participants == 4] <- "Participant 4"

Master$participants <- as.factor(Master$participants)

##Assignment 5
##**************
##From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

Master.dt <- data.table(Master)

##To get  mean of every column broken down by participants and activities

TidyData <- Master.dt[, lapply(.SD, mean), by = 'participants,activities']
write.table(TidyData, file = "Tidy.txt", row.names = FALSE)

Refernces:
Alain F. Zuur A Beginner’s Guide to R book
http://stackoverflow.com/questions/16792737/git-change-working-directory
https://rpubs.com
http://cran.r-project.org/
http://gitref.org/
