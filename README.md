# AdShift
AdShift aims to identify bias in advertising models and diversify their audience targeting strategies to combat such bias. This project was developed in collaboration between students in the Data-X course at UC Berkeley and mentors from Kinesso of Interpublic Group.
We are a team of six UC Berkeley students with various backgrounds and majors. Our team consists of Wei Huang (Materials Science and Engineering), Fernanda Ramos (Data Science), Shaya Barry (Operations Research and Management Sciences), Sneha Sudhakar (Data Science), Zichen Zhao (Economics and Data Science), and Dillon Eskandar (Electrical Engineering and Computer Sciences). In addition, with a diverse list of interdisciplinary experiences including working at a startup, developing API, wrangling with large data sets, research experiences, and so on, we are a well-rounded team who is capable of both qualitative and quantitative projects and has expertise in both the business and technical sides of our work. 

Contact Details:
Wei Huang wei_huang@berkeley.edu
Fernanda Ramos framos0421@berkeley.edu  
Shaya Barry shayabarry@berkeley.edu
Sneha Sudhakar sneha_sudhakar@berkeley.edu
Dillon Eskandar dilloneskandar@berkeley.edu
Zichen Zhao zzc@berkeley.edu

To see news release, please click https://docs.google.com/document/d/11oAe92MfQf1QLMI53FijlpCIBYl6FN3c4cNJ-P6qNhw/edit

## Introduction and Problem
Kinesso is the marketing intelligence engine of Interpublic Group, and as a company, it focuses on using data and technology to improve media and marketing performance. Kinesso directs the purchase of millions of digital advertising impressions every second using advanced targeting strategies. 

One of the major issues with Kinesso’s advertisement targeting model, however, is its inherent bias against various underrepresented populations. This bias problem starts with a lack of true diversity in the population data used as a foundation of the advertisement targeting model. The algorithms used to award advertising space to the highest bidders are influenced by audiences that have a history logging impressions. Combined with the fact that these algorithms are also limited by brand marketing budgets, cheaper ad space for more easily reachable audiences is prioritized over potentially more expensive ads for less represented people groups. This problem is perpetuated by the fact that the model is currently built upon a very complex positive feedback loop. So, while its intention is to maximize a given brand’s return on investment, it continually narrows the group of people it selects from and neglects groups that have been marginalized for various reasons. 

## Solution
AdShift's approach to debiasing has two major components. It first identifies where bias exists within the current advertisement targeting models by comparing demographic datasets to reached datasets on features, such as age, ethnicity, and income. These insights are then used to diversify target audiences, suggesting new opportunities for brands to allocate resources towards underrepresented demographics in the future.

### *Visualizing and Measuring Bias*
In the interactive dashboard created with Tableau (`insert_name`), users have the ability to compare the distribution of impressions to the distribution of households in the demographic data throughout the entirety of the United States. The demographic data can be filtered by age, ethnicity, and income, providing a macro-level view of bias in the reached audience of these advertisements. This dashboard also includes average bias measurements for each state, and the heat map helps demonstrate which states have more underrepresented zip codes and which states have more overrepresented zip codes on average. More detailed introduction could be seen in the `Visualization/UI explanation.pdf` (The data preprocessing codes specifically for visualizations are included in `demo visualization preprocessing.ipynb` and `impression visualization preprocessing.ipynb`.)

To calculate the bias of a particular demographic feature against a zip code, AdShift's algorithm (in `bias_metric_and_diversification_plan.ipynb`) starts by calculating the differences between the "observed" zip code frequencies from the impression data and the "expected" zip code frequencies from the demographic data in a frequency comparison table. Each row of the demographic data for a given zip code contains a unique (age, ethnicity, income) triple and corresponding count of the number of households that meet these criteria. Rows in the impression data, however, do not contain any information about age, ethnicity, or income. With that being said, the algorithm gets the simulated frequency of each (age, ethnicity, income) triple after reweighting by the number of impressions at this zip code instead of by the number of households, and it then samples from these frequencies to generate distributions that can properly be compared on a feature-level. Bias is then measured using one of three metrics: the [Kullback-Leibler Divergence](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence), [Jensen-Shannon Divergence](https://en.wikipedia.org/wiki/Jensen%E2%80%93Shannon_divergence), or [Chi-Squared Test](). These metrics increase in their sensitivity to differences between two distributions as one goes from the Kullback-Leibler Divergence to the Jensen-Shannon Divergence to the Chi-Squared Test, and such sensitivity allows users to customize the aggression with which they want to approach the diversification of their targeting model (discussed below).     

### *Diversifying Target Audiences*
Given a feature and bias metric, AdShift's targeting diversification algorithm (in `bias_metric_and_diversification_plan.ipynb`) measures the amount of money and number of additional impressions needed to lower the bias to a particular threshold for an underrepresented zip code. Users are able to customize various parameters to tune the algorithm to their particular needs:
- `feature`: The demographic feature that the user intends to decrease bias for at the given zip code. At the moment, the possible values are 'age,' 'ethnicity,' and 'income.'
- `bias_metric`: As mentioned above, this determines the sensitivity of the bias calculation, and the user can choose from the Kullback-Leibler Divergence, Jensen-Shannon Divergence, and Chi-Squared Test.
- `max_bias`: This is a threshold used as a stopping criterion for the iterative algorithm.
- `maintain_budget`: 
    - If `False`, this signifies the "unlimited budget option" of the algorithm, in which impressions are increased by `step_size` at each iteration until the bias has decreased past `max_bias`. There is no consideration of the rising costs of targeting the given zip code more aggressively.
    - If `True`, using the money already in the "system," the algorithm iteratively diverts costs and impressions (`step_size` at a time) away from a set of overrepresented zip codes and directs them toward the given underrepresented zip code until the bias against this zip code has decreased past the threshold `max_bias`.
- `step_size`: 
    - If `maintain_budget == False`, this integer corresponds to the number of impressions added to the underrepresented zip code at each iteration of the algorithm.
    - If `maintain_budget == True`, this integer corresponds to the number of impressions taken away from an overrepresented zip code. The number of impressions then added to the underrepresented zip code is calculated using a ratio of the average cost for an ad at the overrepresented zip code over the average cost for an ad at the underrepresented zip code.

## Additional Information
Exploratory data analysis and simple visualizations for the impression dataset and demographic dataset are provided in the files `impression_data_eda.ipynb` and `demo_data_eda.ipynb` respectively, so feel free to view these notebooks to learn more about the structure of the data and its features.

Preliminary exploratory data analysis exists in the deprecated folder (`./[DEPRECATED] international_eda/`) as well, but this examines impression data from the countries of Brazil, France, Germany, and Japan. The dataset initially provided to the AdShift team only contained impressions from foreign countries, and after discovering that demographic data was not easily accessible or up to date on a granular level like zip codes, the team transitioned to working with U.S.-specific impression and demographic data.
