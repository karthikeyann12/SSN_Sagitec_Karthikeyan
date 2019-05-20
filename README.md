# SSN_Sagitec_Karthikeyan
# Classification model with the target of hcpcs_class.

merge <- read_csv("G:/sagitec/merge_na_removed.csv")

mergepop<-merge
summary(mergepop)

mergepop$other[which(is.na(mergepop$other))]<-0
mergepop$therapeutic[which(is.na(mergepop$therapeutic))]<-0
mergepop$total_services_count[which(is.na(mergepop$total_services_count))]<-0
mergepop$total_med_services[which(is.na(mergepop$total_med_services))]<-0
mergepop$line_srvc_cnt[which(is.na(mergepop$line_srvc_cnt))]<-0

mergepop$doc_id<-NULL
mergepop$city<-as.factor(mergepop$city)
mergepop$state<-as.factor(mergepop$state)
mergepop$nppes_provider_city.x<-as.factor(mergepop$nppes_provider_city.x)
mergepop$nppes_provider_state.x<-as.factor(mergepop$nppes_provider_state.x)
mergepop$top1_drug_prescribed<-as.factor(mergepop$top1_drug_prescribed)
mergepop$top2_drug_prescribed<-as.factor(mergepop$top2_drug_prescribed)
mergepop$top3_drug_prescribed<-as.factor(mergepop$top3_drug_prescribed)
mergepop$top4_drug_prescribed<-as.factor(mergepop$top4_drug_prescribed)
mergepop$top5_drug_prescribed<-as.factor(mergepop$top5_drug_prescribed)
mergepop$hcpcs_drug_indicator<-as.factor(mergepop$hcpcs_drug_indicator)
mergepop$hcpcs_class<-as.factor(mergepop$hcpcs_class)

mergepop$city<-NULL
mergepop$state<-NULL
mergepop$nppes_provider_city.x<-NULL
mergepop$nppes_provider_state.x<-NULL
mergepop$top1_drug_prescribed<-NULL
mergepop$top2_drug_prescribed<-NULL
mergepop$top3_drug_prescribed<-NULL
mergepop$top4_drug_prescribed<-NULL
mergepop$top5_drug_prescribed<-NULL
mergepop$hcpcs_code<-NULL
mergepop$hcpcs_description<-NULL
mergepop$hcpcs_chapter<-NULL
mergepop$hcpcs_drug_indicator<-NULL


# correlation plot
correaltion=cor(mergepop[,-51])
corrplot(correaltion)
# Multicollinearity is absent

#Creating train and test dataset from the population
require(caTools)
set.seed(123)
puf_sample<-sample.split(mergepop$hcpcs_class,SplitRatio = .7)
puf_train<-subset(mergepop,puf_sample==T)
puf_test<-subset(mergepop,puf_sample==F)

levels(puf_test$city)<-levels(puf_train$city)
levels(puf_test$state)<-levels(puf_train$state)
levels(puf_test$top1_drug_prescribed)<-levels(puf_train$top1_drug_prescribed)
levels(puf_test$top2_drug_prescribed)<-levels(puf_train$top2_drug_prescribed)
levels(puf_test$top3_drug_prescribed)<-levels(puf_train$top3_drug_prescribed)
levels(puf_test$top4_drug_prescribed)<-levels(puf_train$top4_drug_prescribed)
levels(puf_test$top5_drug_prescribed)<-levels(puf_train$top5_drug_prescribed)
levels(puf_test$nppes_provider_city.x)<-levels(puf_train$nppes_provider_city.x)
levels(puf_test$nppes_provider_state.x)<-levels(puf_train$nppes_provider_state.x)


summary(puf_train)
str(puf_train)
str(puf_test)

# Random Forest
require(randomForest)
r<-randomForest(puf_train$hcpcs_class~.,data = puf_train)

varImp(r)

prediction4<-predict(r,puf_test)
conf4<-confusionMatrix(prediction4,puf_test$hcpcs_class)
conf4

# We can see there is an accuracy of 98%, and kappa value of .84
