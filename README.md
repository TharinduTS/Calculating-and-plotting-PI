#Create vi text files with a list of individuals for each population

# use this script to split all the populations and place them in seperate folders

```bash
declare -a populations=("lab_east_females" "lab_east_females_without_parents" "lab_east_males" "lab_east_males_without_parents" "lab_west_females" "lab_west_females_without_parents" "lab_west_males" "lab_west_males_without_parents" "wild_east_females" "wild_east_males" "wild_west_females" "wild_west_males")
for i in ${populations[@]}; do mkdir ../splitted_populations/$i; done 
for i in ${populations[@]}; do vcftools --vcf ./../full_genome/niger_only_trop.recode.vcf --keep ./../populations/$i --recode --out ../splitted_populations/$i/$i; done
```
#***********Older Method For the same thing*******************************************************

# Calculating-and-plotting-PI
For populations

1.List the samples with

vcf-query -l input.vcf

2. pipe them in to grep to select needed samples
3.use “sed” to take them into 1ine and
4. use tr to remove quotes


# Overall pipe for 1,2,3 and 4
```
vcf-query -l mpileup_raw_wildBorealis_AustinGenome.vcf | grep "Mal" | sed 's/\r//;s/^\|$/"/g'|paste -sd, | tr -d '"'
```

5. this gives names separated by commas
(use this list for the following command)

# 6.Use the following code to filter samples 
```
bcftools view -s Mal_NMK_BJE4563_Xb.fq.gz,xxx,xxxx Borealis_ch8.vcf.recode.vcf > filtered_central_males.vcf
```

# Loop through all populations


# Split using (use 'less <input> | grep chr' first to see whether chromosomes are mentioned same way) 
```
for i in Chr1L Chr1S Chr2L Chr2S Chr3L Chr3S Chr4L Chr4S Chr5L Chr5S Chr6L Chr6S Chr7L Chr7S Chr8L Chr8S Chr9_10L Chr9_10S; do vcftools --vcf mpileup_raw_wildBorealis_AustinGenome.vcf --chr $i --recode --recode-INFO-all --out $i; done
```

# Calculate pi multiple
```
for i in Chr1L Chr1S Chr2L Chr2S Chr3L Chr3S Chr4L Chr4S Chr5L Chr5S Chr6L Chr6S Chr7L Chr7S Chr8L Chr8S Chr9_10L Chr9_10S; do vcftools --vcf $i.recode.vcf --window-pi 50000 --window-pi-step 50000 --out out/$i; done
```
#***************************************************************************************

#split all the populations into seperate chromosomes and storing them inside newly made folders

```bash
declare -a populations=("lab_east_females" "lab_east_females_without_parents" "lab_east_males" "lab_east_males_without_parents" "lab_west_females" "lab_west_females_without_parents" "lab_west_males" "lab_west_males_without_parents" "wild_east_females" "wild_east_males" "wild_west_females" "wild_west_males")
declare -a chromosomes=("chr1L" "chr1S" "chr2L" "chr2S" "chr3L" "chr3S" "chr4L" "chr4S" "chr5L" "chr5S" "chr6L" "chr6S" "chr7L" "chr7S" "chr8L" "chr8S" "chr9_10L" "chr9_10S")
for i in ${populations[@]}; do mkdir ../splitted_populations/$i/splitted; done
for i in ${populations[@]}; do for j in ${chromosomes[@]}; do vcftools --vcf ../splitted_populations/$i/$i\.recode\.vcf --chr $j --recode --recode-INFO-all --out ../splitted_populations/$i/splitted/$j; done; done
~~~

# create plot list
```r
all_populations<-c("females","males")

#**********************************
for (current_pop in all_populations) {
  
  
  #population
  pop<-current_pop
  
  #**********************************
  
  setwd(paste("~/Desktop/OneDrive - McMaster University/Tharindu on Mac/lab/allofraseri/PI/",pop,sep = ''))
  
  
  library(ggplot2)
  plot_list<-list()
  x<-0
  mean_PI<-mean(PI)
  max_PI<-max((PI-mean_PI))
  #remove scientific notation
  options(scipen=999)
  
  ch_numbers<-c("chr1L","chr1S","chr2L","chr2S","chr3L","chr3S","chr4L","chr4S","chr5L","chr5S","chr6L","chr6S","chr7L","chr7S","chr8L","chr8S","chr9_10L","chr9_10S")
  for (i in ch_numbers) {
    chrom_data<-read.table(paste(i,".windowed.pi",sep=''),header = T)
    attach(chrom_data)
    
    x<-x+1
    
    
    
    a<-(ggplot(chrom_data,aes(x=BIN_START,y=(PI-mean_PI),col=(PI-mean_PI)))+
          geom_point()+
          labs(title = paste("PI",current_pop,i))
        +ylim(0, max_PI)
        )
    plot_list[[x]]=a
  }
  
  
  
  library(gridExtra)
  Final_plot_grid<-grid.arrange(
    grobs = plot_list,
    ncol=2
    
    #widths = c(2, 2),
    #heights=c(2,1)
  )
  
  #Sys.sleep(10)
  
  print("done")
  
  
  
  #ggsave(paste("~/Desktop/OneDrive - McMaster University/Tharindu on Mac/lab/Pi/step50000/Borealis/Different_populations/",pop,"/",pop,"_PI_mean_substracted.pdf",sep = ''),plot = Final_plot_grid,width = 15,height = 30)
  #ggsave(paste("~/Desktop/OneDrive - McMaster University/Tharindu on Mac/lab/Pi/step50000/Borealis/Different_populations/all_populations_PI-mean/",pop,"_PI_mean_substracted.pdf",sep = ''),plot = Final_plot_grid,width = 15,height = 30)
  ggsave(paste("~/Desktop/OneDrive - McMaster University/Tharindu on Mac/lab/allofraseri/PI/",pop,"PI.pdf",sep = ''),plot = Final_plot_grid,width = 15,height = 30)
  #*************************************
}
#************************************
```
(use to get y max - max(PI) )

