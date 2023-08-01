# [Developing an Integrated Risk Prediction Tool for Colorectal Cancer](https://jordan-lab.gitbook.io/irt-for-crc/)

## Polygenic risk scores

### Setting up PRSice
To install PRSice (Bash)
```
wget https://github.com/choishingwan/PRSice/releases/download/2.3.5/PRSice_linux.zip
unzip PRSice_linux.zip
```
To get the required scoring files (Bash)
```
wget https://ftp.ebi.ac.uk/pub/databases/spot/pgs/scores/PGS000785/ScoringFiles/PGS000785.txt.gz
gunzip PGS000785.txt.gz
```
Quality control of the scoring files
* To remove metadata (Bash)
```
sed '1,14d' PGS000785.txt > PGS000785.txt`
```
* To set a default p-value of 0 (R)
```
df1 <- read.table("PGS000785.txt", header=TRUE, sep="\t")
df1$P <- '0'
write.table(df1, "PGS000785.txt", quote=FALSE, row.names=FALSE)
```

### Obtaining PRS (Bash)
```
Rscript PRSice/PRSice.R 
--base PGS000785.txt 
--target /home/sharedFolder/referenceData/ukb/imputed_genotypes/ukb_imp_chr#_v3,/home/sharedFolder/referenceData/ukb/imputed_genotypes/ukb_imp_chr1_v3.sample 
--type bgen
--snp 0 --chr 1 --bp 2 --A1 3 --A2 4 --stat 5 --pvalue 10
# --base-maf allelefrequency_effect:0.2
# --maf 0.01
--binary-target T 
--no-clump 
--fastscore
--ignore-fid 
--pheno /home/vsrinivasan75/cases_controls_new/pheno_white.txt
--cov /home/vsrinivasan75/cases_controls_new/covariates_white.txt
--cov-factor SEX 
--quantile 100 --quant-break 10,20,30,40,50,60,70,80,90,100 --quant-ref 50
--out /ukb_prs/colorectal_cancer/using_prsice/PGS000785/PGS000785_results 
--prsice PRSice/PRSice_linux
```

### Analyzing the results
* R2 is obtained from the results of using PRSice (.summary file)
* To compute AUC (R):
```
# Compute AUC for only PRS
auc_prs <- roc(df$PHENO, df$PRS)$auc

# Create GLM for PRS, age, and sex
prs_model <- glm(PHENO ~ PRS + Age + Sex, data = df, family = binomial)

# Get predicted probabilities from the model
predicted_probs <- predict(prs_model, type = "response")

# Compute combined AUC
auc_combined <- roc(df$PHENO, predicted_probs)$auc

# Print combined AUC
print(auc_combined)
```

## Clinical risk factors
The clinical risk factors were compiled using different sources. The dataframe was created by extracting the required columns from the FST file in the Gigalab server (path: /home/sharedFolder/referenceData/ukb/ukb_full.fst). Further, some columns were converted to the desired format.

### Creating the clinical risk model
To create a generalized linear model (GLM) (R):
```
model_clinical <- glm(PHENO ~ Age + Sex + height + weight + physical_activity + meat + smoking + alcohol + father_cancer + mother_cancer + sibling_cancer + polyps + crohns_disease + ulcerative_colitis, data = merged_df, family = binomial)
```

### R-squared (R2)
R-squared can only be obtained for a linear model, so we perform linear regression as follows (R):
```
# Create a model using linear regression
clinical_model <- lm(PHENO ~ Age + Sex + height + weight + physical_activity + meat + smoking + alcohol + father_cancer + mother_cancer + sibling_cancer + polyps + crohns_disease + ulcerative_colitis, data = merged_df, family = binomial)

# Compute R2
clinical_rsq <- summary(clinical_model)$r.squared

# Print R2
print(clinical_rsq)
```

### Area under the curve (AUC)
To compute AUC (R):
```
# Get predicted probabilities
probs_clinical <- predict(model_clinical, type = "response", newdata = merged_df)

# Compute AUC
auc_clinical <- roc(merged_df$PHENO, predict(model_clinical, type = "response"))$auc

# Print AUC
print(auc_clinical)
```

## Integrated risk score
To create the integrated clinical-polygenic model using GLM (R):
```
model <- glm(PHENO ~ PRS + Age + Sex + height + weight + physical_activity + meat + smoking + alcohol + father_cancer + mother_cancer + sibling_cancer + polyps + crohns_disease + ulcerative_colitis, data = merged_df, family = binomial)
summary(model)
```

### Downsizing the number of predictors
To perform stepwise regression to generate the best model with only the most necessary predictors (R):
```
# Create a new GLM model to bypass the error about number of rows changing
model1 <- glm(PHENO ~ PRS + Age + Sex + height + weight + physical_activity + meat + smoking + alcohol + father_cancer + mother_cancer + sibling_cancer + polyps + crohns_disease + ulcerative_colitis, data = model_1$model, family = binomial)

# Run stepwise regression
sml1 <- step(model1)

# View summary of model
summary(sml1)

# Print formula
print(formula(sml1))
```

### Finding the number of extra information added by PRS (R)

* To compute incremental R2:
```
# Create a model using linear regression
integrated_model <- lm(PHENO ~ PRS + Age + Sex + height + weight + physical_activity + meat + smoking + alcohol + father_cancer + mother_cancer + sibling_cancer + polyps + crohns_disease + ulcerative_colitis, data = merged_df, family = binomial)

# Compute integrated R2
integrated_rsq <- summary(integrated_model)$r.squared

# Print incremental R2
incremental_rsq <- integrated_rsq - clinical_rsq
```
* To compute net reclassification index (NRI):
```
# Predict probabilities for integrated model
probs_integrated <- predict(model, type = "response", newdata = merged_df)

# Categorize individuals into risk categories based on the predicted probabilities
categories_without_PRS <- cut(probs_clinical, breaks = c(-Inf, 0.5, Inf), labels = c("Low", "High"))
categories_with_PRS <- cut(probs_integrated, breaks = c(-Inf, 0.5, Inf), labels = c("Low", "High"))

# Create a contingency table of predicted risk categories
contingency_table <- table(categories_clinical, categories_integrated)

# Calculate NRI
NRI <- (sum(contingency_table[1] + contingency_table[2]) - sum(contingency_table[3] + contingency_table[4])) / sum(contingency_table)

# Print NRI
print(NRI)
```
* To compute deltaAUC:
```
# Compute AUC for the integrated model
auc_integrated <- roc(merged_df$PHENO, predict(model), type = "response"))$auc

# Compute deltaAUC
delta_AUC <- auc_integrated - auc_clinical

# Print deltaAUC
print(delta_AUC)
```
