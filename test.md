
```
#### This script is part of the final assignment: "Getting and Cleaning Data Course Project"

# The goal was to transform (summarize) the original data collected from an accelerometers 
# to a tidier format. The main steps are described as following: 
# 0. Read the 'train', 'test' and 'attribute names' data; 
# 1. Merges the training and the test sets to create one data set.
# 2. Extracts only the measurements on the mean and standard deviation for each measurement.
# 3. Uses descriptive activity names to name the activities in the data set
# 4. Appropriately labels the data set with descriptive variable names.
# 5. From the data set in step 4, creates a second, independent tidy data set
# with the average of each variable for each activity and each subject.


# Load the dplyr package
library(dplyr)
#library(plyr)
library(reshape2)

####### 0. read the data files######
# read the attribute's names
attributeNamesTable <- read.table("./UCI HAR Dataset/features.txt", header = FALSE)
# read the activity labels
activityLabelsTable <- read.table("./UCI HAR Dataset/activity_labels.txt", header = FALSE)
### read the training data set
# read the training data
trainData <- read.table("./UCI HAR Dataset/train/X_train.txt", header = FALSE)
# read the training label data
trainLabelData <- read.table("./UCI HAR Dataset/train/y_train.txt", header = FALSE)
# read the training subject data
trainSubjectData <- read.table("./UCI HAR Dataset/train/subject_train.txt", header = FALSE)
### read the test data set
# read the test data
testData <- read.table("./UCI HAR Dataset/test/X_test.txt", header = FALSE)
# read the test label data
testLabelData <- read.table("./UCI HAR Dataset/test/y_test.txt", header = FALSE)
# read the test subject data
testSubjectData <- read.table("./UCI HAR Dataset/test/subject_test.txt", header = FALSE)

##### 1. Merges the training and the test sets to create one data set.
# Merge the training and the test data
totalData <- rbind(trainData, testData)
# Merge the training and the test Label data
totalLabelData <- rbind(trainLabelData, testLabelData)
# Merge the training and the test Subject data
totalSubjectData <- rbind(trainSubjectData, testSubjectData)

##### 3. Uses descriptive activity names to name the activities in the data set
# merge the descriptive activity names with the Label total data
mergeLabelActivityData = merge(totalLabelData, activityLabelsTable, by.x = "V1", by.y = "V1", all.x = TRUE)

##### 4. Appropriately labels the data set with descriptive variable names.
# Assign the attribute names to 'totalData' data.frame 
names(totalData) <- attributeNamesTable[, 2]
# Assign the attribute name to 'totalLabelData'
names(mergeLabelActivityData) <- c("ActivityCode", "ActivityLabel")
# Assign the attribute name to 'totalSubjectData'
names(totalSubjectData) <- c("SubjectCode")

##### 2. Extracts only the measurements on the mean and standard deviation for each measurement.
# index of attribute names with 'mean()' or 'std()' on it  
indexExtractedattributes <- grep("(mean|std)\\(\\)", names(totalData))

# Merge all the tables in only one table
#finalData <- data.frame(totalSubjectData, mergeLabelActivityData[2], totalData[indexExtractedattributes])
finalData <- data.frame(totalSubjectData, mergeLabelActivityData[1], totalData[indexExtractedattributes])
##### 5. From the data set in step 4, creates a second, independent tidy data set with 
#the average of each variable for each activity and each subject.
# group the final data by 'SubjectCode', 'ActivityLabel'
#groupSubjectActivityData <- group_by(finalData, SubjectCode)
#groupSubjectActivityData <- group_by(finalData, SubjectCode, ActivityLabel, add = TRUE)
# summarized the final table by 'SubjectCode', 'ActivityLabel'
#summarizedFinalData <- summarise_each(groupSubjectActivityData, funs(mean))


meltData <- melt(finalData, id=c("SubjectCode", "ActivityCode"))
TidyData <- dcast(meltData, VolunteerID+Activity ~ variable, mean)

x <- dcast(meltData, SubjectCode+ActivityCode ~ variable, mean)

##### Write the final summarized table by 'SubjectCode', 'ActivityLabel'
write.csv(summarizedFinalData, file = "summarizedFinalTable1.csv", row.names=FALSE)
write.table(summarizedFinalData, file = "summarizedFinalTable1.txt", row.name=FALSE) 

```
