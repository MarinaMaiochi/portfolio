Assignment on statistical course with Python

Technologies:
- Python 
    - Pandas
    - Numpy
    - Scipy
___
The live demo can be accessed on this link: https://colab.research.google.com/drive/1EDSRBd5Wg1-k9T_gxZsCzFgMExwVok3F?usp=sharing
___


The complete dataset can be [found here](National_Household_Sample_Survey.csv), it follows this format:

|UF|Sex|Age|Color|Years_of_Study|Income|Height
|---|---|---|---|---|---|---
|11|0|23|8|12|800|1.6038
|11|1|23|2|12|1150|1.7397
|11|1|35|8|15|880|1.7604
___

# <font color='aqua'>Study A</font>

Analyzing our dataset, it is possible to verify that the **proportion of men** as heads of households is almost **70%**. 

**Randomly select groups of 10** to check for differences in earnings in each group. 

### <font color='aqua'>1</font> - What is the **probability that we select a group that has the same proportion of the population**, that is, that we select a group that is **composed of 7 men and 3 women**?
```
probability1 = round((binom.pmf(7,10,0.7)*100),2)   
```
probability1 = 26.68 %

### <font color='aqua'>2</font> - Check the actual male to female ratio in the dataset.
```
total_men = dataset.Sex.value_counts().loc[0]
total_women = dataset.Sex.value_counts().loc[1]
perc_men = total_men / (total_men + total_women)
```
69.3 % of men in the dataset

### <font color='aqua'>3</font> - **How many groups of 10 individuals** would we need to select, at random, to get **100 groups composed of 7 men and 3 women**?

```
probability1 * 100
```
We would need to select 2668.0 groups to get 100 groups composed of 7 men and 3 women.
___

# <font color='aqua'>Study B</font>

A client commissioned us to carry out a study to assess the **income of heads of households in Brazil**. For this we need to perform a new dataset collection, that is, a new field survey. After meeting with the client, it was possible to list the following set of information:

> A. The search result needs to be ready in **2 months**;

> B. We will only have **R$150,000.00** of resources to carry out the field research; and
    
> C. A **error margin not exceeding 10% in relation to the estimated average** would be interesting.

In our experience with studies of this type, we know that the **average cost per individual interviewed is around R$100.00**. With this set of facts, evaluate and obtain the following set of information to pass on to the customer:

### <font color='aqua'>1.</font> To obtain an estimate for the population parameters (Income of heads of household in Brazil), perform a simple random sampling on our dataset. This sample should contain 200 elements. Obtain the mean and standard deviation of this sample.
```
sample = dataset.sample(200)
mean_sample = sample.Income.mean()
std_sample = sample.Income.std()
```
Income → Mean: R$ 1,964.20 | Std: R$ 3,139.89

### <font color='aqua'>2.</font> For the customer specified **margin of error** ,obtain the **sample sizes** necessary to ensure **90%, 95%, and 99% confidence levels**.
```
e1 = 0.10*mean_sample
```
```
z1 = norm.ppf(0.5 + (0.9/2))
n_confidence_90 = (z1 * (std_sample / e1)) ** 2
```
For a 90% confidence level we should select a sample of 691 elements.
```
z2 = norm.ppf(0.5 + (0.95/2))
n_confidence_95 = (z2 * (std_sample / e1)) ** 2
```
For a 95% confidence level we should select a sample of 982 elements.

```
z3 = norm.ppf(0.5 + (0.99/2))
n_confidence_99 = (z3 * (std_sample / e1)) ** 2
```
For a 99% confidence level we should select a sample of 1695 elements.

### <font color='aqua'>3.</font> Get the **research cost** for the three confidence levels.
```
print('Value for a 90% confidence level: R$ {:,.2f} ' .format(n_confidence_90*100))

if n_confidence_90*100 > 150000:
  print('Over the budget')
elif n_confidence_90*100 <= 150000: 
  print('Within the budget')
```
Value for a 90% confidence level: R$ 69,100.00 → Within the budget

Value for a 95% confidence level: R$ 98,200.00 → Within the budget

Value for a 99% confidence level: R$ 169,500.00 → Over the budget

### <font color='aqua'>4.</font> For the highest feasible confidence level (within the available budget), obtain a **confidence interval for the population mean**.
```
interval1 = norm.interval(alpha = 0.95, 
                loc = mean_sample, 
                scale = std_sample / np.sqrt (n_confidence_95))
```
(1767.82, 2160.58)

### <font color='aqua'>5.</font> Assuming the **confidence level chosen in the previous item**, what **error margin** can be considered using all the resources made available by the client?
```
n_confidence_95 = 150000 / 100
z = norm.ppf(.975)
e = z * (std_sample / np.sqrt(n_confidence_95))
e_percentage = e / mean_sample
```
The new error margin is 8.09%.

### <font color='aqua'>6.</font> Assuming a **95% confidence level**, **how much would the survey cost to the client** if it were considered a **error margin of only 5%** in relation to the estimated mean?
```
e = 0.05 * mean_sample
z = norm.ppf(.975)
n_confidence_95 = (z * (std_sample / e)) ** 2
cost_confidence_95 = n_confidence_95 * 100
```
For a 95% confidence level, the cost of the survey would be R$ 392,700.00.

